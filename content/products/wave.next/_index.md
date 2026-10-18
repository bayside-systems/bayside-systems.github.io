---
title: "Wave.next"
description: "Shared document workspace with live CRDT editing, contributor attribution, and configurable agent collaborators."
weight: 1
---

## Shared Document Workspace

Wave.next is a collaborative workspace where people and AI agents write together. Live editing runs on CRDTs (Yjs/Yrs), so two people can edit the same document at once without losing or overwriting each other's changes. Identity, membership, invitations, and chat are handled by a private Matrix server, and document state and history live in PostgreSQL.

If your team needs to write together — or you want AI agents to contribute to shared documents, Wave.next is that workspace, local-first: the whole stack (web app, Matrix, PostgreSQL, CRDT sync) runs on your own hardware.

### Why CRDT Collaboration?

Traditional editors and collaboration tools were not designed for concurrent human-agent writing. Treating simultaneous edits like single-user documents is a category error.

The risks are concrete:

- **Conflicting edits** that get lost or overwritten under concurrent writing
- **Opaque authorship** where you cannot tell who wrote what
- **Unattributed agent contributions** that blur the line between human and model text
- **Fragile history** where past revisions cannot be inspected

Wave.next removes these risks at the data-structure layer: CRDTs merge concurrent edits deterministically, and every text region keeps its author.

### What Wave.next Provides

**Live CRDT Editing**  
Edits are Yjs updates synced over an authenticated WebSocket. The sync service persists every delta and snapshot in PostgreSQL, broadcasts each committed update to all participants, and supports catch-up from any sequence. Full history is retained.

**Contributor Attribution**  
Each text region carries its author's identity as a shared CRDT attribute. Quiet background tints and a contributor legend show who wrote what, and attribution survives concurrent editing, formatting, and reloads.

**Invitations and Workspaces**  
Create a workspace, share its code, and invite members through Matrix invitations. Membership controls access to documents, chat, and compute backends.

**Agent Collaborators**  
Add an agent from the Collaborate panel: name, chat-completions endpoint, model, optional API key, and instructions. The agent joins the workspace with its own identity and, when addressed in the discussion pane, receives the current document and returns a revised version through its own sync session.

**Discussion and Presentation**  
Every document has an artifact-scoped discussion pane: messages are stored in the database, visible to workspace members, and can be inserted into the document as an attributed note. The read-only presentation view renders the live document into a print-ready paper layout, with LaTeX source export.

**Code Workspaces**  
Paste a public HTTPS git URL, pick a branch, and import the repository into a code-editing workspace with a foldable file tree and a monospace editor. Imported snapshots go to a pluggable object store; deleting the workspace removes the files, chat, and CRDT history.

**In-Browser SSH Terminal**  
The SSH client runs in the browser and connects to a machine you already run — the host running Claude, Codex, Grok, OpenCode, or a plain shell. The API is a member-authorized WebSocket-to-TCP relay.

**Computelet Binding**  
Code and finance spaces can bind to an already-running Bayside Virtualization Platform computelet. The browser opens a Ghostty terminal to the bound guest, and enrollment uses the guest's own capability announcement rather than hypervisor credentials.

### Local-First Deployment

The complete stack runs on your own hardware: Docker Compose builds the web app, the private Matrix server, the PostgreSQL database, and the CRDT sync service. First run provisions local TLS certificates and an HTTPS endpoint (port 8443 by default). Your documents, members, and agent configuration stay local by default — hosted operation is an option, not the default.

### Use Cases

- Team and research writing with full revision history and per-author attribution
- Agent-assisted documents: address an agent from the discussion pane to organize, format, or expand content
- Code collaboration: import public git repositories and edit code together
- Multi-workspace research groups with invitations and per-workspace compute pools
- Paper workflows: render the live document to a presentation layout and export the LaTeX source

---

**Ready to write together?**  
[Contact us](/contact) to discuss Wave.next deployment options.
