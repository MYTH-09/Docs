## 1. The View Layer: Rendering & Interaction

> **Used :** _HTML5 Canvas via `Konva.js` (Specifically `react-konva`)_

###### The Problem with raw HTML5 Canvas (`ctx.lineTo()`) :

In native HTML5 Canvas when you write code to draw a circle, the browser colors the pixels on the screen and then _instantly forgets_ that a circle exists. It only sees a grid of colored pixels.

- **The Nightmare:** If a user clicks the screen, the browser only tells you "the user clicked pixel X:50, Y:50." If you want them to drag that circle, you have to write intense mathematical formulas (**raycasting**) to calculate if their mouse click actually intersected with the pixels that make up your circle. You also have to calculate the **bounding boxes** (the invisible square border around a shape) to know when a user is hovering over its edge to resize it.

**1. Why Konva.js?** Konva upgrades your canvas it introduces an **Object Model** (often called a "Scene Graph").

- Instead of just blindly painting pixels, Konva keeps a memory of the things you draw (e.g., "Shape 1 is a Red Rectangle").
    
- Because Konva remembers the objects, it completely handles the math for **click-detection**, dragging, and rotation. When a user clicks the red rectangle, Konva automatically knows they clicked it. You don't have to write any complex geometry code.
>[!info]-
>- **Tree Hierarchy:** Every item is an object (a **Node**). A main stage holds layers, layers hold groups, and groups hold individual shapes (like rectangles or text).
>	
>- **Transformation Inheritance:** If you move, scale, or rotate a parent Group, all the child shapes inside it inherit that movement automatically.
>	
>- **Retained Mode (Memory):** Unlike a standard HTML5 canvas that paints pixels and immediately forgets them, a scene graph keeps a persistent memory of every object and its properties.
>	
>- **Smart Hit Detection & Event Bubbling:** Because it "remembers" objects, it knows exactly which shape you clicked. Just like web pages, click events can "bubble up" from a shape to its parent group.
>	
>- **Selective Rendering:** If you change one shape, the system doesn't redraw the whole screen. It isolates the exact layer that changed and only updates that part, making it incredibly fast.

**2. Why React-Konva?** Usually, when an object moves on a canvas, you have to write an **imperative render loop**: you have to tell the computer step-by-step to wipe the entire screen completely clean, and then meticulously redraw every single shape in its new position, 60 times a second.

- `react-konva` lets you write **declarative** code. Just like you write standard React HTML (`<div>`), you just declare your shapes (`<Rect x={10} y={20} />`).
    
- When your data changes (say, the user drags the rectangle to `x={30}`), React-Konva automatically manages the messy render loop behind the scenes. Your code stays clean and simple.

_**The Alternatives**_

- **Fabric.js:** This does the exact same thing as Konva (it provides an Object Model). It is very powerful, especially for image manipulation, but its underlying code architecture is a bit older, which can make tying it into modern React apps slightly more cumbersome.
    
- **Rough.js:** This is the engine Excalidraw uses. It makes lines look like they were drawn by a human with a pen (the "sketchy" look). However, Rough.js is _only_ a renderer—it does **not** have an Object Model. It doesn't know what a bounding box is or how to drag shapes. If you use it, you have to build all the click-detection math yourself from scratch!

>[!info]
>Excalidraw team built their own "Scene Graph" and "Hit Detection" engine around Rough.js... 💀💀



___

## 2. The Synchronization Layer: Handling Conflicts

> _**Used : `Yjs` (CRDT Library)**_

Do not try to write your own conflict resolution algorithm. Use a CRDT.

- **Why Yjs?** Yjs is the undisputed king of collaborative web data structures right now. It is incredibly fast and highly optimized for network size.
    
- **How it works:** You create a `Y.Map` or `Y.Array` to store your canvas objects (e.g., an array of shape data: `{ id: 1, type: 'rect', x: 10, y: 20 }`). When a user drags a shape, you update the Yjs data structure. Yjs automatically calculates the exact delta, handles any conflicts if someone else moved it, and gives you the final merged state.

- Yjs abstracts away all the heavy lifting—handling UUIDs, vector clocks, operation-based packets, and LWW tie-breakers out of the box. You just tell Yjs, "the user moved this shape," and it handles the complex sync logic for you.

> [!info]-
> ### 1. Unique IDs (The Foundation)
> 
> Instead of tracking items by their position in a list (which changes when people delete things), every shape or letter gets a **Globally Unique Identifier (UUID)** and a **Peer ID** (who created it).
> 
> - **Engineering Term:** _Idempotent addressing._
>     
> - **In practice:** Updates don't say "Move the first shape." They say "Move shape `#7A9F`."
>     
> 
> ### 2. Logical Clocks (Solving Time)
> 
> Physical device clocks cannot be trusted to sync events. CRDTs use **Logical Clocks** (like **Lamport Clocks** or **Vector Clocks**), which are simply counters that tick up every time an action happens.
> 
> - **Engineering Term:** _Causality tracking._
>     
> - **In practice:** It numbers events like comic book panels. Event #5 always happens after Event #4, establishing a perfect timeline without ever looking at the real world time.
>     
> 
> ### 3. State-Based vs. Operation-Based (The Two Flavors)
> 
> - **State-Based (CvRDT):** You send the entire state of your board to your peer, and they merge it with theirs. Reliable, but heavy on bandwidth.
>     
> - **Operation-Based (CmRDT):** You only send the specific action ("Shape `#7A9F` moved right by 10px"). This is what high-performance libraries like **Yjs** use because it is incredibly lightweight and fast.
>     
> 
> ### 4. Last-Write-Wins (Conflict Resolution)
> 
> When two users edit the exact same property at the exact same logical time, CRDTs use a deterministic tie-breaker called **Last-Write-Wins (LWW)**.
> 
> - **Engineering Term:** _Deterministic conflict resolution._
>     
> - **In practice:** If logical and physical times tie, the system looks at the alphabetical order of the users' **Peer IDs**. Because every device runs the exact same math, they all instantly agree on the winner without needing a central server.

___

## 3. The Transport Layer: Real-Time Communication

**Used : PartyKit**

- **The DIY Route (`y-websocket`):** Self-hosting on a raw server is free (minus server costs) but requires dealing with a major DevOps headache to handle crashes, disconnects, and scaling.
    
- **The Managed Route (Liveblocks):** Highly convenient with zero backend work and pre-built features, but the free tier is too restrictive (max 100 users) and max 10 users per room.
    
- **The Serverless Route (PartyKit):** Powered by Cloudflare's global edge, it offers massive scale for thousands of users completely free, though it requires a small amount of backend code and an external database to save permanent room states.

___

# Layer 4: The Surrounding Infrastructure

To turn a raw canvas into a fully functioning web application, you need four surrounding pieces. Here is the recommended stack to keep it 100% free and easy to build.

## 1. The Building Structure (Frontend Framework)

**Recommendation: Next.js (Hosted on Vercel)**

- **What it does:** Next.js is a framework built on top of React. While React-Konva handles the canvas, Next.js handles the rest of the webpage (the toolbars, the home page, the settings menu).
    
- **The technical reason:** Next.js handles **Routing** effortlessly. You want your friends to be able to go to a URL like `yourapp.com/board/room-123`. Next.js reads that URL, extracts the `room-123` part, and tells PartyKit to connect the user to that specific room.
    
- **Why it fits your goal:** Vercel (the company behind Next.js) will host your frontend 100% for free with zero configuration.
    

## 2. The Short-Term Memory (Local State Management)

**Recommendation: Zustand**

- **What it does:** The Yjs engine remembers the _canvas_ (what shapes exist). But your app also needs to remember the _UI state_ (e.g., "Is the user currently holding the pen tool or the eraser tool?", "Is the color picker open?").
    
- **The technical reason:** You need a **Global State Manager**. If a user clicks the "Red Color" button in the top toolbar, the Canvas component at the bottom of the screen needs to know about it.
    
- **Why not Redux or React Context?** Redux requires writing hundreds of lines of complex "boilerplate" code. React Context can cause your entire app to lag and re-render every time you switch tools. **Zustand** is a tiny, modern library that lets you create a global state variable in 3 lines of code without any performance lag.
    

## 3. The Vault (Database & Persistence)

**Recommendation: Supabase (PostgreSQL)**

- **What it does:** It stores your data permanently. Remember, PartyKit is a temporary server—it wipes its memory completely clean every 24 hours on the free tier. If you don't have a database, your friends' drawings will vanish the next day.
    
- **The technical reason:** Yjs handles the live syncing, but it also has a feature that allows you to compress the entire whiteboard into a tiny, efficient binary file called a `Uint8Array` (a raw blob of 1s and 0s).
    
- **How they work together:**
    
    1. When your friends finish drawing and leave the room, PartyKit takes that binary blob and saves it into a column in your Supabase Postgres database.
        
    2. Tomorrow, when a friend opens the link again, PartyKit grabs the blob from Supabase, unpacks it back into Yjs, and the whiteboard reappears exactly as they left it.
        
- **Why it fits your goal:** Supabase is the best open-source alternative to Firebase. It gives you a massive, powerful PostgreSQL database entirely for free.
    

## 4. The Bouncer (Authentication)

**Recommendation: Supabase Auth**

- **What it does:** It lets your friends log in securely using Google, GitHub, or an Email/Password so they can have a personal dashboard of "My Saved Boards."
    
- **The technical reason:** Handling passwords, security cookies, and login sessions from scratch is a massive security risk. **OAuth** (Open Authorization) allows users to log in with Google without you ever seeing their password.
    
- **Why it fits your goal:** Since you are already using Supabase for your database, you can use their built-in Authentication system. It is extremely easy to set up, and it automatically connects your users' accounts to their saved whiteboards in the database.
    

### The Final, Complete Stack :

1. **Frontend UI:** Next.js + Tailwind CSS + Zustand
    
2. **Canvas Rendering:** React-Konva
    
3. **Conflict Resolution:** Yjs
    
4. **Multiplayer Network:** PartyKit
    
5. **Database & Login:** Supabase