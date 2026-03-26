# Splunk Architecture Builder

A single-file, browser-based diagramming tool for building and documenting Splunk data architecture. No installation, no server, no dependencies. Open the HTML file and start building.

---

## Table of Contents

- [Overview](#overview)
- [Getting Started](#getting-started)
- [Interface Layout](#interface-layout)
- [Building a Diagram](#building-a-diagram)
  - [Infrastructure Nodes](#infrastructure-nodes)
  - [Custom Nodes](#custom-nodes)
  - [Data Feeds](#data-feeds)
  - [Connections](#connections)
- [Working with the Diagram](#working-with-the-diagram)
  - [Navigation](#navigation)
  - [Moving Nodes](#moving-nodes)
  - [Deleting Nodes](#deleting-nodes)
  - [Arrow Routing](#arrow-routing)
- [Saving and Loading](#saving-and-loading)
- [Exporting](#exporting)
- [Feed Inventory Table](#feed-inventory-table)
- [Tips](#tips)

---

## Overview

The Splunk Architecture Builder is a standalone HTML tool designed to help Splunk architects and engineers visually document data flows from sources through forwarders to indexers. It supports all standard Splunk infrastructure node types, custom vendor nodes (Cribl, Splunk HEC, cloud platforms, etc.), labeled connections, and a full data feed inventory table.

<!-- screenshot: full application overview -->

---

## Getting Started

1. Download `splunk_diagram_builder.html`
2. Open it in any modern browser (Chrome, Firefox, Edge)
3. The example diagram loads automatically. Use it as a starting point or click **✕ Clear** to start fresh

---

## Interface Layout

The application has two tabs accessible from the header:

| Tab | Purpose |
|-----|---------|
| **Edit** | Add and configure all nodes, feeds, and connections |
| **Diagram** | View, navigate, and interact with the rendered diagram |

The header toolbar contains all major actions:

| Button | Action |
|--------|--------|
| ⟳ Render | Renders the diagram from current Edit tab data |
| ↓ Diagram SVG | Exports the diagram canvas as an SVG file |
| ↓ Table SVG | Exports the feed inventory table as an SVG file |
| ↓ Feed CSV | Exports the feed inventory as a CSV file |
| 💾 Save | Saves the full project state as a `.splunkarch.json` file |
| 📂 Load | Loads a previously saved `.splunkarch.json` file |
| ↺ Reset Layout | Clears all manual node positions and resets to auto-layout |
| ⊞ Example | Loads the built-in example diagram |
| ✕ Clear | Clears everything and starts fresh |

The **Title** and **Subtitle** fields at the top of the Edit tab control the diagram header. Changes reflect live in the diagram while on the Diagram tab.

---

## Building a Diagram

All content is defined in the **Edit** tab, which is split into two columns:

- **Left column** — Infrastructure nodes (Splunk components and data sources)
- **Right column** — Data feeds and labeled connections

### Infrastructure Nodes

Click the **+** button for each node type to add it. Each node has a **Name**, **Role**, and **Port** field. Use the **✕** button on a card to remove it.

| Node Type | Color | Typical Use |
|-----------|-------|-------------|
| Management | Cyan | Cluster Manager, License Master, Deployment Server, Monitoring Console |
| Indexer | Green | Splunk Indexers |
| Search Head | Green | Splunk Search Heads |
| Heavy Forwarder | Yellow | HF nodes for syslog aggregation or protocol transformation |
| Universal Forwarder | Yellow | UF agents on endpoints |
| Data Source | Blue | Network devices, applications, servers sending data |
| Custom / External | Custom color | Cribl, AWS, Syslog relays, Splunk HEC — see [Custom Nodes](#custom-nodes) |
| Index Group | Muted | Documents index names for a logical group |

<img width="1904" height="1032" alt="image" src="https://github.com/user-attachments/assets/a6532201-9885-48a2-8ee1-c08dab33467f" />

### Custom Nodes

Custom nodes support four visual shapes, each suited to different use cases:

| Shape | Intended Use |
|-------|-------------|
| **Cylinder** | Databases, storage systems, Syslog servers |
| **Cloud** | AWS, Azure, GCP, SaaS platforms |
| **Splunk** | Splunk HEC or other Splunk-branded components |
| **Cribl** | Cribl Stream, Cribl Edge, Cribl Lake |

Each custom node has a **Name**, **Role**, **Port**, and a **color picker** to match your organization's color conventions.

<!-- screenshot: Custom node cards and shape picker -->

### Data Feeds

Data feeds represent the flow of log data from a source to a forwarder and optionally to indexers. Each feed captures:

| Field | Description |
|-------|-------------|
| Feed Name | Descriptive name (e.g. `Cisco ASA Syslog`) |
| Sourcetype | Splunk sourcetype (e.g. `cisco:asa`) |
| Protocol | Transport protocol (e.g. `Syslog`, `TCP`, `UDP`) |
| Port | Listening port |
| Index | Destination Splunk index |
| Source | The originating node |
| Forwarder | The receiving Heavy or Universal Forwarder |
| Indexer(s) | Target indexers (multi-select) |
| Notes | Free-text notes (e.g. `via RBSYSLOG`) |

Feed arrows are rendered as **green dashed lines** on the diagram and populate the feed inventory table below the diagram.

<!-- screenshot: Data feed card in Edit tab -->

### Connections

Connections represent infrastructure-level relationships between nodes — cluster management, deployment, replication, search, etc. Each connection has:

| Field | Description |
|-------|-------------|
| From | Source node |
| To | Destination node |
| Label | Short description (e.g. `cluster mgmt`) |
| Port | Port number |
| Style | `Dashed`, `Solid`, or `Dotted` |

Connection arrows are color-coded by the destination node type and display the label and port at the midpoint of the line.

<!-- screenshot: Connection card and rendered arrow with label -->

---

## Working with the Diagram

### Navigation

| Action | Result |
|--------|--------|
| **Scroll** | Zoom in / out |
| **Click and drag** on empty canvas | Pan the diagram |
| **+** / **−** buttons | Step zoom in / out |
| **⌂** button | Reset zoom and pan to default |

### Moving Nodes

| Action | Result |
|--------|--------|
| **Click** a node | Select it (highlighted with cyan glow) |
| **Shift+click** a node | Add to / remove from selection |
| **Drag** a selected node | Move it — arrows update in real time |
| **Drag** with multiple nodes selected | Move the whole group together |
| **↺ Reset Layout** | Snap all nodes back to auto-calculated positions |

<!-- screenshot: Node selected with cyan highlight, being dragged -->

### Deleting Nodes

Hover over any node on the diagram to reveal a **red × button** in its top-right corner. Click it to immediately remove the node, its feed references, and any connections attached to it. The Edit tab and feed inventory table update automatically.

<!-- screenshot: Delete button appearing on node hover -->

### Arrow Routing

Arrows are routed automatically using an orthogonal elbow algorithm. The exit and entry sides of each arrow are chosen independently based on the relative position of the two connected nodes:

- **Node to the right** → arrow exits right, enters left
- **Node below** → arrow exits bottom, enters top
- **Directly opposite faces** → clean straight line or simple jog
- **Same-direction conflict** → U-turn route around the outside of both nodes

Arrows update in real time as you drag nodes around the canvas.

---

## Saving and Loading

### Save

Click **💾 Save** to save the complete project state. A prompt will ask for a filename — the diagram title is pre-filled as a suggestion. The file saves as `<filename>.splunkarch.json`.

The save file captures everything:

- All node names, roles, and ports
- All custom node shapes and colors
- All data feeds and connections
- All manual node positions from dragging
- Title and subtitle

### Load

Click **📂 Load** and select a `.splunkarch.json` file. The entire project is restored exactly as it was saved, including manual positions.

> **Tip:** The `.splunkarch.json` format is plain readable JSON — it can be version-controlled in Git alongside your Splunk configs.

---

## Exporting

Three export formats are available from the header toolbar:

| Export | Format | Contents |
|--------|--------|---------|
| ↓ Diagram SVG | `.svg` | The full diagram canvas as a vector image, suitable for documentation or presentations |
| ↓ Table SVG | `.svg` | The feed inventory table as a standalone dark-themed vector image |
| ↓ Feed CSV | `.csv` | All feed inventory data as a spreadsheet-compatible CSV |

SVG exports can be imported into Confluence, inserted into PowerPoint, or opened in Inkscape or Illustrator for further editing.

<img width="1914" height="1027" alt="image" src="https://github.com/user-attachments/assets/cecaf7c5-2bd2-4c25-be6e-aacede6c6436" />

---

## Feed Inventory Table

The feed inventory table is displayed below the diagram canvas and lists all data feeds that have at least a name or source node defined. It can be toggled open/closed with the **▲/▼** button and resized using the **A+/A−** font size buttons.

| Column | Contents |
|--------|---------|
| Feed Name | Name of the data feed |
| Source | Originating node |
| Sourcetype | Splunk sourcetype |
| Protocol | Transport protocol |
| Port | Listening port |
| Forwarder | Receiving forwarder node |
| Indexer(s) | Target indexers |
| Index | Destination index |
| Notes | Free-text notes |

<!-- screenshot: Feed inventory table with sample data -->

---

## Tips

- Use **⊞ Example** to explore the tool before building your own diagram
- The **Title** and **Subtitle** fields update the diagram header live — no re-render needed
- Node positions are preserved across renders — use **↺ Reset Layout** if the layout gets cluttered
- Custom node colors can be matched to your organization's vendor color conventions
- Save files are plain JSON and can be committed to Git alongside your Splunk configuration files
