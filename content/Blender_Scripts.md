UP: [[Software-Home]]
## Curve to Json
```
import bpy
import json  
  output_data = {}  
  for obj in bpy.context.selected_objects:  
      if obj.type == 'CURVE':  
          splines = []  
          for spline in obj.data.splines:  
              points = []  
              if spline.type == 'BEZIER':  
                  for p in spline.bezier_points:  
                      co = obj.matrix_world @ p.co  
                      points.append({"x": co.x, "y": co.y, "z": co.z})  
              else:  
                  for p in spline.points:  
                      co = obj.matrix_world @ p.co.xyz  
                      points.append({"x": co.x, "y": co.y, "z": co.z})  
              splines.append({  
                  "type": spline.type,  
                  "cyclic": spline.use_cyclic_u,  
                  "points": points  
              })  
          output_data[obj.name] = splines  
		# Save JSON
  output_path = bpy.path.abspath("//selected_splines.json")  
  with open(output_path, 'w') as f:  
      json.dump(output_data, f, indent=4)  
  print(f"Exported splines from {len(output_data)} object(s) to {output_path}")```
```
## Vertices to curves
```python
import bpy
# --- User Settings ---
# Set to True if you want the curve to loop back to the start
MAKE_CYCLIC = False
# ---------------------
# Get all currently selected objects
selected_objects = bpy.context.selected_objects
if not selected_objects:
    print("Error: No objects selected. Please select one or more mesh objects.")
else:
    newly_created_curves = []
    original_objects = []
    # Loop over every selected object
    for obj in selected_objects:
        original_objects.append(obj) # Store for later deselection
        # --- Validation for each object ---
        if obj.type != 'MESH':
            print(f"Skipping '{obj.name}': Not a Mesh.")
            continue # Skip to the next object in the loop
        if not obj.data.vertices:
            print(f"Skipping '{obj.name}': Mesh has no vertices.")
            continue # Skip to the next object
        # --- Main Logic (now inside the loop) ---
        # 1. Get the vertices from the mesh
        # We sort them by index to connect them in the order they were created.
        # This is the most reliable "order" we can get.
        vertices = sorted(obj.data.vertices, key=lambda v: v.index)
        # 2. Get vertex coordinates in WORLD space
        # This is crucial! It accounts for the object's location, rotation, and scale.
        # We use obj.matrix_world to transform each vertex's local coordinate (v.co)
        world_coords = [(obj.matrix_world @ v.co) for v in vertices]
        # 3. Create a new Curve data-block
        curve_data = bpy.data.curves.new(name=f"{obj.name}_CurveData", type='CURVE')
        curve_data.dimensions = '3D'
        # 4. Create a new spline (the actual line) in the curve data
        # We use 'POLY' for a simple, straight-line connection (connect-the-dots)
        spline = curve_data.splines.new('POLY')
        # 5. Add the required number of points to the spline
        spline.points.add(len(world_coords))
        # 6. Assign the vertex coordinates to the spline points
        # Note: Spline points have 4 components (x, y, z, w). 'w' is weight, set to 1.
        for i, coord in enumerate(world_coords):
            spline.points[i].co = (coord[0], coord[1], coord[2], 1.0)
        # 7. Set the spline to cyclic (closed loop) if requested
        spline.use_cyclic_u = MAKE_CYCLIC
        # 8. Create a new Object to hold the curve data
        curve_obj = bpy.data.objects.new(name=f"{obj.name}_Curve", object_data=curve_data)
        # 9. Link the new curve object to the scene collection
        bpy.context.collection.objects.link(curve_obj)
        newly_created_curves.append(curve_obj) # Add to our list
        print(f"Successfully created '{curve_obj.name}' from {len(vertices)} vertices.")
    # --- After the loop ---
    # 10. Deselect all original objects
    for obj in original_objects:
        if obj.select_get():
            obj.select_set(False)
    # 11. Select and activate all new curves
    if newly_created_curves:
        for curve_obj in newly_created_curves:
            curve_obj.select_set(True)
        # Make the last created curve the active object
        bpy.context.view_layer.objects.active = newly_created_curves[-1]
        print(f"Finished. Created {len(newly_created_curves)} curve(s).")
    else:
        print("Finished. No valid mesh objects were found to convert.")
```

## Remove every other loop
```import bpy
import bmesh

def main():
    print("--- IY Loop Reducer: Final Attempt ---")

    # 1. Get Object
    obj = bpy.context.active_object
    if not obj or obj.type != 'MESH':
        print("Error: Select a mesh object.")
        return

    # 2. Ensure Edit Mode
    if obj.mode != 'EDIT':
        bpy.ops.object.mode_set(mode='EDIT')
    
    # Force Edge Selection Mode
    bpy.context.tool_settings.mesh_select_mode = (False, True, False)

    # 3. FIX: Manually update BMesh history
    # This ensures 'loop_multi_select' knows which edge is the "Active" one.
    me = obj.data
    bm = bmesh.from_edit_mesh(me)
    
    selected_edges = [e for e in bm.edges if e.select]
    if not selected_edges:
        print("Error: No edges selected.")
        return

    # Force the first selected edge to be the active one
    bm.select_history.clear()
    bm.select_history.add(selected_edges[0])
    bmesh.update_edit_mesh(me)

    # 4. Find the correct 3D Viewport
    area = None
    region = None
    
    for win in bpy.context.window_manager.windows:
        for a in win.screen.areas:
            if a.type == 'VIEW_3D':
                area = a
                for r in a.regions:
                    if r.type == 'WINDOW':
                        region = r
                        break
                break
        if area: break

    if not area:
        print("Error: No 3D Viewport found.")
        return
    
    print(f"DEBUG: Using Area: {area.type}, Region: {region.type}")

    # 5. Run Commands with Override
    override = {
        'window': bpy.context.window,
        'screen': bpy.context.window.screen,
        'area': area,
        'region': region,
        'scene': bpy.context.scene,
        'active_object': obj,
        'edit_object': obj,
        'selected_editable_objects': [obj],
    }

    try:
        with bpy.context.temp_override(**override):
            
            # STEP A: Use 'loop_multi_select' instead of 'select_edge_ring'
            # This is the more robust, modern command for "Select Ring"
            bpy.ops.mesh.loop_multi_select(ring=True)
            
            # STEP B: Checker Deselect
            bpy.ops.mesh.select_nth(nth=2, offset=0)
            
            # STEP C: Select Edge Loops
            bpy.ops.mesh.loop_multi_select(ring=False)
            
            # STEP D: Dissolve
            bpy.ops.mesh.dissolve_edges(use_verts=True)

        print("Success: Loop Reducer finished.")

    except Exception as e:
        print(f"FAILED: {e}")

if __name__ == "__main__":
    main()
```
## Pipes on walls
```bl_info = {
    "name": "Ortho Pipe Generator",
    "author": "Gemini",
    "version": (3, 0),
    "blender": (5, 0, 0),
    "location": "View3D > Sidebar (N) > Ortho Pipes",
    "description": "Converts Annotations into arrays of filleted 90-degree pipes.",
    "category": "Add Mesh",
}

import bpy
import math

def rdp(points, epsilon):
    if len(points) < 3:
        return points
        
    dmax = 0.0
    index = 0
    end = len(points) - 1
    
    p0 = points[0]
    pend = points[end]
    line_vec = pend - p0
    
    if line_vec.length < 0.0001:
        for i in range(1, end):
            d = (points[i] - p0).length
            if d > dmax:
                index = i
                dmax = d
    else:
        line_dir = line_vec.normalized()
        for i in range(1, end):
            v = points[i] - p0
            d = v.cross(line_dir).length
            if d > dmax:
                index = i
                dmax = d
                
    if dmax > epsilon:
        res1 = rdp(points[:index+1], epsilon)
        res2 = rdp(points[index:], epsilon)
        return res1[:-1] + res2
    else:
        return [p0, pend]

def make_ortho_path(points, step_size, fillet_radius, simplification):
    if not points:
        return []
        
    simplified_pts = rdp(points, simplification)
    
    if len(simplified_pts) < 2:
        return simplified_pts
        
    result = [simplified_pts[0].copy()]
    current_pt = simplified_pts[0].copy()
    
    safe_dist = max(step_size, fillet_radius * 1.5)
    
    for i in range(1, len(simplified_pts)):
        pt = simplified_pts[i]
        delta = pt - current_pt
        
        axes = [('x', delta.x, pt.x), ('y', delta.y, pt.y), ('z', delta.z, pt.z)]
        axes.sort(key=lambda item: abs(item[1]), reverse=True)
        
        for axis, dist, target_val in axes:
            if abs(dist) < max(safe_dist, simplification):
                continue
                
            setattr(current_pt, axis, target_val)
            if (current_pt - result[-1]).length > 0.01:
                result.append(current_pt.copy())
                
    if len(result) < 3:
        return result
        
    cleaned = [result[0]]
    for i in range(1, len(result)-1):
        diff1 = result[i] - cleaned[-1]
        diff2 = result[i+1] - result[i]
        
        if diff1.length < 0.001 or diff2.length < 0.001:
            continue
            
        if abs(diff1.normalized().dot(diff2.normalized())) > 0.999:
            continue
            
        cleaned.append(result[i])
    cleaned.append(result[-1])
    
    return cleaned

def create_pipe_geometry_nodes(tree_name, pipe_radius, fillet_radius, fillet_segments, pipe_count, pipe_spacing, wall_offset, profile_twist):
    if tree_name in bpy.data.node_groups:
        bpy.data.node_groups.remove(bpy.data.node_groups[tree_name])
    gn_tree = bpy.data.node_groups.new(tree_name, 'GeometryNodeTree')
    
    if hasattr(gn_tree, "interface"):
        gn_tree.interface.new_socket(name="Geometry", in_out='INPUT', socket_type='NodeSocketGeometry')
        gn_tree.interface.new_socket(name="Geometry", in_out='OUTPUT', socket_type='NodeSocketGeometry')

    node_in = gn_tree.nodes.new('NodeGroupInput')
    node_out = gn_tree.nodes.new('NodeGroupOutput')
    
    # 1. Main Path Nodes
    node_to_bezier = gn_tree.nodes.new('GeometryNodeCurveSplineType')
    if hasattr(node_to_bezier, "spline_type"):
        node_to_bezier.spline_type = 'BEZIER'
        
    node_fillet = gn_tree.nodes.new('GeometryNodeFilletCurve')
    for inp in node_fillet.inputs:
        if 'Radius' in inp.name and 'Limit' not in inp.name:
            inp.default_value = fillet_radius
        elif 'Limit' in inp.name:
            inp.default_value = True

    node_res = gn_tree.nodes.new('GeometryNodeSetSplineResolution')
    for inp in node_res.inputs:
        if 'Resolution' in inp.name:
            inp.default_value = fillet_segments
            
    node_curve_to_mesh = gn_tree.nodes.new('GeometryNodeCurveToMesh')
    
    # --- 2. THE NEW ARRAY & OFFSET PROFILE SYSTEM ---
    
    # Generate the base circle
    node_circle = gn_tree.nodes.new('GeometryNodeCurvePrimitiveCircle')
    for inp in node_circle.inputs:
        if 'Radius' in inp.name: inp.default_value = pipe_radius
        elif 'Resolution' in inp.name: inp.default_value = 16

    # Generate the mathematical line to space the array evenly
    start_x = -(pipe_count - 1) * pipe_spacing / 2.0
    
    node_line = gn_tree.nodes.new('GeometryNodeMeshLine')
    for inp in node_line.inputs:
        if inp.name == 'Count': 
            inp.default_value = pipe_count
        elif inp.name == 'Start Location': 
            # wall_offset moves the profile in the Y axis, perfectly resting it on the surface
            inp.default_value = (start_x, wall_offset, 0.0) 
        elif inp.name == 'Offset': 
            inp.default_value = (pipe_spacing, 0.0, 0.0)
            
    # Instance the circle onto the array line
    node_instance = gn_tree.nodes.new('GeometryNodeInstanceOnPoints')
    node_realize = gn_tree.nodes.new('GeometryNodeRealizeInstances')
    
    # Allow the user to spin the entire array if the normals misbehave
    node_transform = gn_tree.nodes.new('GeometryNodeTransform')
    for inp in node_transform.inputs:
        if 'Rotation' in inp.name:
            inp.default_value = (0.0, 0.0, profile_twist)

    # Link Main Path
    gn_tree.links.new(node_in.outputs[0], node_to_bezier.inputs[0])
    gn_tree.links.new(node_to_bezier.outputs[0], node_fillet.inputs[0])
    gn_tree.links.new(node_fillet.outputs[0], node_res.inputs[0])
    gn_tree.links.new(node_res.outputs[0], node_curve_to_mesh.inputs[0])
    
    # Link Profile Array
    gn_tree.links.new(node_line.outputs[0], node_instance.inputs['Points'])
    gn_tree.links.new(node_circle.outputs[0], node_instance.inputs['Instance'])
    gn_tree.links.new(node_instance.outputs[0], node_realize.inputs[0])
    gn_tree.links.new(node_realize.outputs[0], node_transform.inputs[0])
    
    # Plug Array into Curve Sweep
    gn_tree.links.new(node_transform.outputs[0], node_curve_to_mesh.inputs['Profile Curve'])
    gn_tree.links.new(node_curve_to_mesh.outputs[0], node_out.inputs[0])
    
    return gn_tree

class CURVE_OT_annotation_to_ortho(bpy.types.Operator):
    bl_idname = "curve.annotation_to_ortho"
    bl_label = "Convert to 90° Curve"
    bl_description = "Converts Annotations into filleted 3D orthogonal pipe arrays."
    bl_options = {'REGISTER', 'UNDO'}
    
    stroke_simplification: bpy.props.FloatProperty(
        name="Stroke Simplification",
        description="Merges wobbly lines into straight paths.",
        default=10.0,
        min=0.0
    )
    
    step_size: bpy.props.FloatProperty(
        name="Turn Threshold",
        description="Minimum distance before turning 90 degrees.",
        default=150.0,
        min=0.0
    )
    
    pipe_thickness: bpy.props.FloatProperty(
        name="Pipe Radius",
        description="Radius of the generated pipe.",
        default=10.0,
        min=0.0
    )
    
    fillet_radius: bpy.props.FloatProperty(
        name="Corner Rounding",
        description="How much to smooth the 90-degree corners.",
        default=20.0,
        min=0.0
    )
    
    fillet_segments: bpy.props.IntProperty(
        name="Corner Segments",
        description="Number of edges to smooth out the corners.",
        default=4,
        min=1
    )
    
    # --- NEW ARRAY AND OFFSET PARAMETERS ---
    wall_offset: bpy.props.FloatProperty(
        name="Wall Offset",
        description="Pushes the pipe away from the surface. Match to Pipe Radius to be flush.",
        default=10.0, # Defaults to 10 to match the default pipe_thickness
    )
    
    pipe_count: bpy.props.IntProperty(
        name="Number of Pipes",
        description="Generates an array of parallel pipes.",
        default=1,
        min=1,
        soft_max=20
    )
    
    pipe_spacing: bpy.props.FloatProperty(
        name="Pipe Spacing",
        description="Distance between parallel pipes.",
        default=25.0,
        min=0.0
    )
    
    profile_twist: bpy.props.FloatProperty(
        name="Profile Twist",
        description="Rotates the pipes around the path. Use 90, 180, 270 if pipes sink into the wall.",
        default=0.0,
        subtype='ANGLE'
    )
    
    @classmethod
    def poll(cls, context):
        if hasattr(bpy.data, "annotations"):
            return len(bpy.data.annotations) > 0
        return False
        
    def execute(self, context):
        anno_data = None
        
        if hasattr(bpy.data, "annotations") and len(bpy.data.annotations) > 0:
            anno_data = bpy.data.annotations[0]
                
        if not anno_data or not anno_data.layers:
            self.report({'WARNING'}, "No annotations found. Draw something first!")
            return {'CANCELLED'}

        curve_data = bpy.data.curves.new('OrthoPipes_Data', type='CURVE')
        curve_data.dimensions = '3D'
        curve_obj = bpy.data.objects.new('OrthoPipes', curve_data)
        context.collection.objects.link(curve_obj)
        
        strokes_found = False
        
        for layer in anno_data.layers:
            if not layer.active_frame: continue
            for stroke in layer.active_frame.strokes:
                points = [p.co for p in stroke.points]
                ortho_pts = make_ortho_path(points, self.step_size, self.fillet_radius, self.stroke_simplification)
                
                if len(ortho_pts) < 2:
                    continue
                    
                strokes_found = True
                spline = curve_data.splines.new('POLY')
                spline.points.add(len(ortho_pts) - 1)
                
                for i, pt in enumerate(ortho_pts):
                    spline.points[i].co = (pt.x, pt.y, pt.z, 1.0)
                    
        if not strokes_found:
            bpy.data.objects.remove(curve_obj)
            bpy.data.curves.remove(curve_data)
            self.report({'WARNING'}, "No drawn strokes found in the active frame.")
            return {'CANCELLED'}
                
        bpy.ops.object.select_all(action='DESELECT')
        curve_obj.select_set(True)
        context.view_layer.objects.active = curve_obj
        
        gn_mod = curve_obj.modifiers.new(name="Fillet_and_Thickness", type='NODES')
        gn_tree = create_pipe_geometry_nodes(
            "Ortho_Pipe_Nodes", 
            self.pipe_thickness, 
            self.fillet_radius, 
            self.fillet_segments,
            self.pipe_count,
            self.pipe_spacing,
            self.wall_offset,
            self.profile_twist
        )
        gn_mod.node_group = gn_tree
            
        return {'FINISHED'}

class VIEW3D_PT_ortho_pipes(bpy.types.Panel):
    bl_space_type = 'VIEW_3D'
    bl_region_type = 'UI'
    bl_category = 'Ortho Pipes'
    bl_label = "Pipe Generator"
    
    def draw(self, context):
        layout = self.layout
        layout.operator("curve.annotation_to_ortho", text="Convert Annotations", icon='CURVE_PATH')

classes = (
    CURVE_OT_annotation_to_ortho,
    VIEW3D_PT_ortho_pipes,
)

def register():
    for cls in classes:
        bpy.utils.register_class(cls)

def unregister():
    for cls in reversed(classes):
        bpy.utils.unregister_class(cls)

if __name__ == "__main__":
    register()`