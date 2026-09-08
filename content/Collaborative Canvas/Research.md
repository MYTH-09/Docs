A collaborative whiteboard relies on three core architectural pillars: **The View Layer** (how drawing happens), **The Transport Layer** (how messages move), and **The Synchronization Layer** (how everyone stays in sync without breaking things).

## 1. The View Layer: Rendering the Canvas

The browser needs to capture user input (mouse, touch, stylus) and render shapes instantly. There are two primary web technologies used for this:

#### HTML5 Canvas API (Bitmaps)

- **How it works:** It provides a blank pixel grid. When a user drags a mouse, JavaScript commands explicitly paint pixels onto this bitmap (e.g., `ctx.lineTo(x, y); ctx.stroke();`).
    
- **Pros:** Extremely performant. It can handle tens of thousands of active particles or strokes without breaking a sweat because the browser only remembers pixels, not objects.
    
- **Cons:** No built-in memory of shapes. Once a line is drawn, the canvas doesn't know it's a "line"—it's just colored pixels. To implement scaling, zooming, moving, or deleting specific shapes, you must keep an in-memory data structure of your elements and entirely re-render the canvas whenever something changes.

#### SVG (Scalable Vector Graphics)

- **How it works:** Elements are rendered as true XML DOM nodes (e.g., `<circle cx="50" cy="50" r="40" />`).
    
- **Pros:** Native event handling per shape (e.g., clicking a specific line to delete it), infinite scaling without pixelation, and easy manipulation via CSS/JS.
    
- **Cons:** Performance degrades rapidly as the number of elements increases. Thousands of complex vector paths will bloat the DOM and slow the browser down.
    

> **Industry Standard:** Most professional whiteboards (like Figma or Miro) use **HTML5 Canvas** or even custom **WebGL/WebGPU** contexts for performance, paired with an underlying data array representing the shapes.


--- 


## 2. The Transport Layer: Real-Time Communication

Traditional HTTP requests (GET/POST) are client-driven and pull-based, making them completely inadequate for live collaboration. Whiteboards require immediate, low-latency, push-based communication.

- **WebSockets:** The backbone of most collaborative apps. It establishes a single, persistent, bi-directional TCP connection between each client and a central server. When Client A draws a line, an event payload is sent to the server, which immediately blasts it out to Clients B, C, and D.
    
- **WebRTC:** A peer-to-peer alternative. Instead of routing traffic through a central server, clients connect directly to each other. While it offers incredibly low latency, managing state synchronization becomes highly complex when handling many users.

## 3. The Synchronization Layer: Handling Conflicts

The hardest part of a collaborative app is ensuring that **Client A** and **Client B** see the exact same thing, even when they draw over the same spot at the exact same moment across a high-latency network.

If two users move the same circle simultaneously, a naive system will jitter wildly or create divergent states. To solve this, collaborative apps use specialized data synchronization algorithms:

#### Operational Transformation (OT)

- **Concept:** A centralized approach where a server receives drawing actions ("operations"), sequences them chronologically, transforms them if they conflict, and broadcasts the corrected sequence to all clients.
    
- **Used by:** Google Docs.
    

#### Conflict-free Replicated Data Types (CRDTs)

- **Concept:** A decentralized approach where data structures are designed so that operations can be applied in any order, by any client, concurrently, and they will naturally converge to the exact same state without a master server dictating the order. Each element (like a line or sticky note) is assigned a globally unique ID and a cryptographic timestamp or vector clock.
    
- **Used by:** Figma, Miro (often utilizing libraries like `Yjs` or `Automerge`).
	_More about it later...._

# Study of Already Existing Tools and there Architecture.


## Excalidraw: The Client-Heavy Approach

Excalidraw pushes the computational heavy lifting to the client's browser, using the server primarily as a dumb relay. This makes it incredibly fast, open-source friendly, and capable of end-to-end encryption.

### 1. The View Layer

- **Technology:** React + HTML5 Canvas (2D Context).
  **Rough.js** (for rendering) combined with a _**Custom Object Model & Hit Detection Engine**._

- **How it works:** Instead of treating the screen like a static image, Excalidraw keeps a hidden list of every shape you draw (e.g., _Rectangle at X:10, Y:20, Color: Blue_).

- Every single time you move, resize, or delete a shape, Excalidraw quickly wipes the whole screen blank and redraws everything from scratch based on that list.
    
- **Why it's fast:** Because the shapes are simple math vectors, modern web browsers can wipe and redraw the screen 60 times a second. It happens so fast your eyes just see smooth movement.

### 2. The Transport Layer

	Excalidraw is built for ultimate privacy (End-to-End Encryption).
	
- **Technology:** WebSockets.
    
- **How it works:** When you draw a circle, your computer locks that data in a digital safe (encrypts it) before sending it out.
    
- **The Server's Job:** The central server receives this locked safe and forwards it to your teammates. The server has no idea what’s inside—it doesn't know if you drew a circle, a square, or wrote a secret plan. It just delivers the package. Your teammates' computers have the key to unlock it.

### 3. The Synchronization Layer

- **Technology:** Element-level CRDTs (Conflict-free Replicated Data Types) via a Last-Write-Wins (LWW) mechanism.
    
- **The Concept:** The "Highest Version Wins" rule.

	Since the server is "blind" and can't referee who made a change first, the computers collaborating on the drawing have to sort out disagreements themselves. They use a system called **CRDT** with a **Last-Write-Wins** rule.

- **Editing a shape:**  Every drawing element is assigned a globally unique ID, a `version` number, and a `versionNonce`. When you scale a shape, its version increments. When a client receives a broadcasted element from a teammate, it compares the incoming version with its local version. The element with the highest version number automatically overwrites the older one.
>[!info]-
>To understand **`versionNonce`**, it helps to first understand what a standard **nonce** is in computer science.
>
>The word "nonce" stands for **"number used once."** It is a unique, random string of numbers or letters generated for a specific action to prevent confusion or security issues.
>
>In Excalidraw, the `versionNonce` works alongside the `version` number as a tie-breaker to handle a very specific problem: **simultaneous edits**.
>
>**_The Problem: The Double-Edit Deadlock_**
>	Imagine you and a teammate are collaborating on a drawing. You both have a square on your screens, and it is currently at `version: 1`.
>	
>	At the exact same millisecond, without the network knowing yet:
>	1. **You** change the square's color to **Blue**. Your computer bumps it to   `version: 2`.
>	2. **Your teammate** changes the same square's color to **Red**. Their computer _also_ bumps it to  `version: 2`.
>	
>Now, both computers receive an update that has the exact same version number  (`version: 2`).
>	
>	Because Excalidraw's rule is "Highest Version Wins," the computers get stuck. They can't decide if Blue or Red should win because `2` equals `2`. If different computers make different guesses, your screens will fall out of sync.
>	
>**_The Solution: The Tie-Breaker (`versionNonce`)_**
>
>To fix this, every time a shape is edited, Excalidraw doesn't just increase the version number; it also generates a completely random **`versionNonce`** (like a fingerprint for that specific edit).
>
>Now look at the same scenario with a `versionNonce` added:
>- **Your edit:** `version: 2`, `versionNonce: 84729` (Blue)  
>- **Teammate's edit:** `version: 2`, `versionNonce: 11503` (Red)  
>
>When the conflict happens, the computers see that the version numbers are tied at `2`. So, they look at the `versionNonce` as a tie-breaker.
>
>The system uses a simple mathematical rule, like _"whichever random nonce number is higher wins."_ Since `84729` is bigger than `11503`, your Blue square wins on both screens.

- **Deleting a shape:** If you delete a circle, Excalidraw doesn't actually erase it from the code right away. Instead, it slaps a digital sticky note on it that says `"isDeleted: true"` (called a **tombstone**). This stops a slow internet connection from accidentally re-sending the circle back to your screen a few seconds later like a ghost.

	
	![[Pasted image 20260707183416.png]]

## Miro: The Server-Authoritative Approach

Miro operates at a massive scale, supporting hundreds of concurrent users moving objects on effectively infinite canvases. Relying on clients to handle state for a board with 50,000 objects would melt a browser, so Miro relies on a highly intelligent backend.

### 1. The View Layer

- **Technology:** HTML5 Canvas  with WebGL acceleration. _**100% Proprietary**_
    
- **WebGL Acceleration:** Instead of forcing your computer's main processor (CPU) to do all the heavy lifting, Miro hands the drawing job over to your graphics card (GPU). Your GPU is built specifically for video games and heavy graphics, making zooming and panning feel buttery smooth.
#### Spatial Indexing & Viewport-Aware Rendering (The "Brain")

To stay fast, Miro needs to be smart about _what_ it draws. It uses two clever tricks:

##### Quad-Trees (Spatial Indexing)

Instead of searching through a messy pile of 10,000 sticky notes to find the ones you are looking at, Miro organizes the board like a neatly filed cabinet using a structure called a **Quad-Tree**.

- It divides your infinite board into four massive quadrants.
    
- If a quadrant has too stuff in it, it splits that quadrant into four smaller squares, and so on.
    
- This allows Miro's engine to instantly say: _"Okay, the user is looking at Square A4. Ignore everything in squares B, C, and D."_

##### Viewport-Aware Rendering

Your **viewport** is simply the boundaries of your current screen.

- **If you can't see it, Miro doesn't draw it.** 
   If you zoom into the top-left corner to read one specific sticky note, Miro completely pauses and "unloads" the thousands of other sticky notes, charts, and arrows sitting in the bottom-right corner. They still exist in the memory, but the graphics engine completely ignores them until you scroll over to them.

### 2. The Transport Layer

- **Technology:** WebSockets
	
- At the foundational protocol level, Miro and Excalidraw — both rely on **WebSockets** to maintain an open, real-time connection. 
- **Excalidraw (The Courier):** The WebSocket carries encrypted, opaque blobs.
- **Miro :** The WebSocket carries readable, specific commands (e.g., "Move Object X"). The server opens the message, validates it, dictates the chronological order of events, and _then_ broadcasts an authoritative update to all clients.

### 3. The Synchronization Layer

- **Technology:** Centralized Operation-Based Sync
	
- **How it works:** Unlike Excalidraw, Miro does not let clients decide who wins a conflict. The central server is the absolute source of truth. Each operation sent by a client carries a sequence number. The server receives these operations from all users, sequences them chronologically, resolves any spatial conflicts, and assigns a global sequence number before broadcasting. If you and a teammate attempt to scale the same image simultaneously, the server dictates the order of execution to ensure every client converges to the exact same state.

	![[Pasted image 20260707210359.png]]

**OT - Operational Transformer**
It allows multiple people to edit the same document at the same time (like Google Docs) without overriding or messing up each other's work. It intercepts everyone's edits, calculates the differences, and instantly rewrites the incoming instructions so they fit together perfectly.


### Architecture Summary

|       **Feature**       |              **Excalidraw**               |                      **Miro**                       |
| :---------------------: | :---------------------------------------: | :-------------------------------------------------: |
| **Rendering Strategy**  |       Canvas 2D (Full Scene Redraw)       |   WebGL + Canvas 2D (Viewport-Aware, Quad-Trees)    |
|     **Server Role**     |      "Dumb" Relay (routes messages)       |  "Smart" Authority (sequences and logs operations)  |
| **Conflict Resolution** |    Client-side CRDT (Last-Write-Wins)     |            Server-side Operational Sync             |
|   **Security Model**    |      Supports End-to-End Encryption       |           Server must decrypt to process            |
|   **Ideal Use Case**    | Fast, lightweight sketching & wireframing | Enterprise diagrams, infinite persistent workspaces |
