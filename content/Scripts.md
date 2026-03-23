# Get all selected curves
```
selected_curves = cmds.ls(selection=True, type="transform")
if not selected_curves:
    cmds.warning("No curves selected! Please select curves before running.")
else:
    for curve in selected_curves:
        # Create a NURBS circle for profile
        profile = cmds.circle(radius=0.2, normal=(0,1,0), constructionHistory=False)[0]  # Adjust radius if needed
        # Move the profile to the start of the curve
        start_pos = cmds.pointPosition(curve + ".cv[0]")  # Get first CV of the curve
        cmds.move(start_pos[0], start_pos[1], start_pos[2], profile)
        # Extrude the profile along the curve
        cmds.select(profile, curve)
        extruded = cmds.extrude(profile, curve, useComponentPivot=1, fixedPath=True, useProfileNormal=True, polygon=1)
        # Delete the profile curve after extrusion
        cmds.delete(profile)
    print("Extrusion completed on all curves!")
```
# Convert locator to group
```
import maya.cmds as cmds
import re
def convert_locators_to_groups():
    # Get all locator shape nodes
    locator_shapes = cmds.ls(type='locator', long=True)
    for locator_shape in locator_shapes:
        # Get the transform node
        locator_transform = cmds.listRelatives(locator_shape, parent=True, fullPath=True)[0]
        # Get clean name for later renaming (no path, just basename)
        base_name = locator_transform.split('|')[-1]
        # Get children (transform nodes only)
        children = cmds.listRelatives(locator_transform, children=True, type='transform', fullPath=True) or []
        # Get parent to preserve hierarchy
        parent = cmds.listRelatives(locator_transform, parent=True, fullPath=True)
        # Get world transform values
        pos = cmds.xform(locator_transform, query=True, worldSpace=True, translation=True)
        rot = cmds.xform(locator_transform, query=True, worldSpace=True, rotation=True)
        scl = cmds.xform(locator_transform, query=True, worldSpace=True, scale=True)
        # Create a new group (temporary name to avoid name clash)
        temp_group = cmds.group(empty=True, name=base_name + "_TEMP_REPLACE")
        # Apply world transforms
        cmds.xform(temp_group, worldSpace=True, translation=pos)
        cmds.xform(temp_group, worldSpace=True, rotation=rot)
        cmds.xform(temp_group, worldSpace=True, scale=scl)
        # Reparent children
        for child in children:
            cmds.parent(child, temp_group)
        # Reparent group to the locator's original parent
        if parent:
            cmds.parent(temp_group, parent[0])
        # Delete locator transform (which also deletes its locator shape)
        cmds.delete(locator_transform)
        # Rename group to the original locator's name
        cmds.rename(temp_group, base_name)
    print("✅ All locators converted to groups.")
convert_locators_to_groups()
```
# Convert doublesmooth to nurbs
```
import maya.cmds as cmds
def poly_to_nurbs_replace_original():
    sel = cmds.ls(selection=True, long=True)
    if not sel:
        cmds.warning("No mesh selected.")
        return
    for obj in sel:
        base_name = obj.split('|')[-1]
        print("Processing: {}".format(base_name))
        # Store original parent
        original_parent = cmds.listRelatives(obj, parent=True, fullPath=True)
        if original_parent:
            original_parent = original_parent[0]
        else:
            original_parent = None
        # Rename original mesh temporarily
        temp_name = cmds.rename(obj, base_name + "_TO_DELETE")
        # List all transforms before
        before_transforms = set(cmds.ls(type="transform"))
        # Convert to SubD
        subd_result = cmds.polyToSubdiv(temp_name,
                                        ap=0,
                                        ch=False,
                                        aut=True,
                                        maxPolyCount=100000,
                                        maxEdgesPerVert=32)
        if not subd_result:
            cmds.warning("SubD conversion failed for {}".format(temp_name))
            continue
        subd_shape = subd_result[0]
        subd_transform = cmds.listRelatives(subd_shape, parent=True, fullPath=True)[0]
        # Convert to NURBS
        cmds.subdToNurbs(subd_transform,
                         ch=False,
                         aut=True,
                         ot=0)
        # List all transforms after
        after_transforms = set(cmds.ls(type="transform"))
        new_transforms = list(after_transforms - before_transforms)
        # Filter for NURBS transforms only
        nurbs_transforms = []
        for t in new_transforms:
            shapes = cmds.listRelatives(t, shapes=True, fullPath=True) or []
            for s in shapes:
                if cmds.objectType(s) == "nurbsSurface":
                    nurbs_transforms.append(t)
                    break  # Only need one NURBS shape per transform
        if not nurbs_transforms:
            cmds.warning("No NURBS surfaces found.")
            continue
        print("  Found {} NURBS patches.".format(len(nurbs_transforms)))
        # If only one, just rename it directly
        if len(nurbs_transforms) == 1:
            final_name = cmds.rename(nurbs_transforms[0], base_name)
            if original_parent:
                cmds.parent(final_name, original_parent)
                print("  Re-parented to: {}".format(original_parent))
        else:
            # Group them and rename the group
            group = cmds.group(nurbs_transforms, name=base_name)
            if original_parent:
                cmds.parent(group, original_parent)
                print("  Re-parented group to: {}".format(original_parent))
        # Cleanup
        cmds.delete(temp_name)
        if cmds.objExists(subd_transform):
            cmds.delete(subd_transform)
        print("  Cleanup complete for: {}".format(base_name))
poly_to_nurbs_replace_original()
```
# Export 3xf
```
import maya.cmds as cmds
import maya.mel as mel
import os
def export_selected_soft_objects_to_3xf():
    # Ask for export folder
    export_dir = cmds.fileDialog2(dialogStyle=2, fileMode=3, caption="Choose Export Folder")
    if not export_dir:
        cmds.warning("Export cancelled.")
        return
    export_dir = export_dir[0].replace("\\", "/")
    print("Exporting to folder: {}".format(export_dir))
    selected = cmds.ls(selection=True, long=True)
    if not selected:
        cmds.warning("No objects selected.")
        return
    for obj in selected:
        name = obj.split('|')[-1]
        if not name.startswith("SOFT_"):
            print("Skipping (does not start with SOFT_): {}".format(name))
            continue
        # Convert name for filename (underscores to dots)
        filename = name.replace("_", ".") + ".3xf"
        export_path = os.path.join(export_dir, filename).replace("\\", "/")
        # Select only this object
        cmds.select(obj, r=True)
        # MEL export command
        export_cmd = (
            'file -force -options '
            '"globalFrame=0;exportFrame=0;importFrame=0;treatFileUnitsAs=1;'
            'exportMaterialsAs=2;exportLights=0;exportCameras=1;exportNormals=1;'
            'export3DExciteObjectNames=1;rotationOffset=0=0=0;translationOffset=0=0=0;'
            'exportOptimizeMeshes=0;texEvaluate=0;importOptimize=0;importOptimizeVertTol=0;'
            'importOptimizeNormTol=0;importIgnoreUVs=0;importLodChildren=1;importLodIndex=1;'
            'importIgnoreShadowUVs=0;importUniqueNames=0;ignoreHiddenObject=0;'
            'importCameraSetup=0;exportOptimizeNormalTol=0;importVertexMergeTolerance=0.001;'
            'importGeometryCleanUp=0;automaticMaterialAssignment=0;AttachVariantsOnly=0;" '
            '-typ "3XF_3DEXCITE" -pr -es "{}";'
        ).format(export_path)
        try:
            mel.eval(export_cmd)
            print("Exported: {}".format(export_path))
        except Exception as e:
            cmds.warning("Export failed for {}: {}".format(name, str(e)))
    cmds.select(clear=True)
    print("✅ All exports complete.")
# Run it
export_selected_soft_objects_to_3xf()
```
# Export hierarchy
```
import maya.cmds as cmds
import maya.mel as mel
import os
def export_empty_hierarchy_fbx():
    path = cmds.fileDialog2(fileMode=0, caption="Export FBX Hierarchy", fileFilter="FBX Files (*.fbx)")
    if not path:
        return
    path = path[0]
    if not path.lower().endswith(".fbx"):
        path += ".fbx"
    if cmds.objExists("TEMP_EXPORT_ROOT"):
        cmds.delete("TEMP_EXPORT_ROOT")
    temp_root = cmds.group(em=True, name="TEMP_EXPORT_ROOT")
    top_nodes = cmds.ls(assemblies=True, long=True)
    for node in top_nodes:
        if node == "|TEMP_EXPORT_ROOT":
            continue
        dup = cmds.duplicate(node, returnRootsOnly=True)[0]
        cmds.parent(dup, temp_root)
        shapes = cmds.listRelatives(dup, allDescendents=True, fullPath=True, type=["mesh", "nurbsSurface", "nurbsCurve"]) or []
        for shape in shapes:
            try:
                if cmds.objExists(shape):
                    cmds.delete(shape)
            except Exception as e:
                print("⚠️ Could not delete shape:", shape, "→", e)
    cmds.select(temp_root, hierarchy=True)
    mel.eval('FBXExportBakeComplexAnimation -v false')
    mel.eval('FBXExportConstraints -v false')
    mel.eval('FBXExportSkeletonDefinitions -v false')
    mel.eval('FBXExportInputConnections -v false')
    mel.eval('FBXExportCameras -v false')
    mel.eval('FBXExportLights -v false')
    mel.eval('FBXExportShapes -v false')
    mel.eval('FBXExportSkins -v false')
    # 🔐 Safely export with clean double quotes
    export_cmd = 'FBXExport -f "{}" -s'.format(path.replace("\\", "/"))
    mel.eval(export_cmd)
    cmds.delete(temp_root)
    print("✅ Exported hierarchy-only FBX to:", path)
# Run it:
export_empty_hierarchy_fbx()
```