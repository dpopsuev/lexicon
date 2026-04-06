---
id: 3d-mesh-terminology
title: "3D Mesh Terminology"
description: "Industry-standard polygon and volume mesh vocabulary. Canonical terms for vertices, edges, faces, cells, and prisms."
labels: [pragmatic, geometry, 3d, mesh, vocabulary]
always_apply: false
---

# 3D Mesh Terminology

Industry-standard vocabulary for polygon and volume meshes. Use these terms in code, comments, docs, specs, and contracts. References: Wikipedia Polygon Mesh, Types of Mesh (FEM/CFD literature).

## Surface Mesh Elements

| Term | Definition | Vertex Count |
|------|-----------|-------------|
| **Vertex** | Position in 3D space with optional attributes (color, normal, UV, blend weights) | 1 |
| **Edge** | Connection between two vertices | 2 |
| **Face** | Closed set of edges forming a 2D polygon. Triangle (3 edges), quad (4 edges), or n-gon. The atomic 2D rendering unit. | 3+ |
| **Surface mesh** | Collection of faces defining the shape of a polyhedral object's surface. No interior. | - |

## Volume Mesh Elements

| Term | Definition | Vertices | Edges | Faces |
|------|-----------|----------|-------|-------|
| **Cell** | 3D volumetric element bounded by faces. The atomic 3D volume unit. Synonyms: element (FEM). | varies | varies | varies |
| **Tetrahedron** | Simplest cell. 4 triangular faces. Easy to auto-generate. | 4 | 6 | 4 tri |
| **Hexahedron** | Brick/cube cell. 6 quadrilateral faces. Highest accuracy in FEM. | 8 | 12 | 6 quad |
| **Prism (Wedge)** | Triangular prism. 2 triangular end-faces + 3 quadrilateral side-faces. Efficient boundary layer resolution. | 6 | 9 | 5 (2 tri + 3 quad) |
| **Pyramid** | Transition element. 1 quadrilateral base + 4 triangular sides. Bridges structured/unstructured regions. | 5 | 8 | 5 (1 quad + 4 tri) |
| **Volume mesh** | Collection of cells explicitly representing both surface and interior of a structure. | - | - | - |

## Mesh Structure Terms

| Term | Definition |
|------|-----------|
| **Node** | Vertex in simulation context (FEM, ECS). Interchangeable with vertex for positional data. |
| **Connectivity** | The neighborhood relationships between elements. Which cells share faces, edges, or vertices. |
| **Adjacency** | Which elements are neighbors. Lateral (same level) or vertical (stacked). |
| **Topology** | The arrangement and relationship structure of elements, independent of geometry. |
| **Manifold** | A mesh where every edge is shared by exactly 2 faces (closed) or 1 face (boundary). Well-formed meshes are manifold. |
| **Shell** | A connected set of faces forming a closed or open surface. The boundary of a solid. |
| **Normal** | A vector perpendicular to a face, used for lighting and orientation. |

## Mesh Representations

| Representation | Storage | Trade-off |
|---------------|---------|-----------|
| **Face-vertex** | Faces reference vertex indices. Most common. GPU-native. | Fast rendering, O(1) vertex lookup. |
| **Winged-edge** | Each edge points to 2 vertices, 2 faces, 4 adjacent edges. | O(1) traversal, high memory. |
| **Half-edge** | Each half-edge stores next, prev, opposite, vertex, face. | Good traversal, moderate memory. |

## Naming Conventions

- Use **face** not "facet" or "kite" for 2D polygons bounding a cell or on a surface.
- Use **cell** not "wedge" or "element" for the atomic 3D volume unit.
- Use **prism** when specifying the cell shape (triangular prism = 6 vertices, 5 faces).
- Use **vertex** not "point" or "position" for mesh vertices.
- Use **volume mesh** not "3D mesh" or "solid mesh" for meshes with explicit interior.
- Use **surface mesh** not "2D mesh" or "shell mesh" for face-only meshes.
- Qualify "triangle" always: "GPU triangle" (rendering), "Delaunay triangle" (tessellation), "face" (mesh element).

## Hegemony Domain Mapping

These domain terms are Hegemony-specific and layer on top of industry mesh vocabulary:

| Domain Term | Definition | Mesh Equivalent |
|------------|-----------|-----------------|
| **Tile** | Gameplay atom. Hexagonal or pentagonal region at one layer in a column. | Collection of cells at one layer |
| **Column** | Vertical stack of tiles at one globe position. | Column of cells along radial axis |
| **Layer** | Horizontal classification: Ground (always), Water (optional), Subsurface (future). | Subset of cells at same radial depth |

Domain terms describe gameplay semantics. Industry terms describe geometry. Both vocabularies coexist:
- "A tile is composed of 6 prism cells" (domain + industry)
- "Each cell has 5 faces: 2 triangular end-faces and 3 quadrilateral side-faces" (pure industry)
- "The water layer sits above the ground layer in each column" (pure domain)
