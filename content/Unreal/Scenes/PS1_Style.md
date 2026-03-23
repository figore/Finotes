# Scene parameters
- ![[image_1760510285113_0.png]]
- Skylight can be stationary to remove AO, also disable realtime capture and set it to cubemap and pick a daylight map
# Vertex snapping
- create a Material Parameter Collection (will control a parameter between different master materials)
- name it MPC_Global
- open it and make a new scalar value - VertexSnapAmount
- set it to 0.3
- create material function MF_vertex_snapping
- open and drag in the MPC_Global
- on the left switch the name to the one you made
- add world position offset and set it to camera based
- ![[Pasted image 20251223132245.png]]
- Now you can add this function to world position offset slot of any master material