import bpy

# Import the shirt glTF file
bpy.ops.import_scene.gltf(filepath="path/to/shirt.gltf")

# Get the imported object
obj = bpy.context.selected_objects[0]

# Set the pivot point to the bottom of the object
bpy.context.scene.cursor.location = obj.matrix_world @ obj.data.vertices[0].co
bpy.ops.object.origin_set(type='ORIGIN_CURSOR')

# Set the origin to the bottom of the object
bpy.ops.object.editmode_toggle()
bpy.ops.mesh.select_all(action='SELECT')
bpy.ops.mesh.remove_doubles()
bpy.ops.object.editmode_toggle()
bpy.ops.object.origin_set(type='ORIGIN_GEOMETRY')

# Define the fold planes
fold_planes = [
    {'axis': 'Y', 'direction': -1, 'position': 0.25},
    {'axis': 'Y', 'direction': 1, 'position': -0.25},
    {'axis': 'X', 'direction': -1, 'position': 0.25},
    {'axis': 'X', 'direction': 1, 'position': -0.25},
    {'axis': 'Z', 'direction': -1, 'position': 0.25},
    {'axis': 'Z', 'direction': 1, 'position': -0.25},
]

# Fold the object along each plane
for plane in fold_planes:
    # Create a plane object to use as the folding plane
    bpy.ops.mesh.primitive_plane_add(size=2, enter_editmode=False, location=(0, 0, 0))
    plane_obj = bpy.context.selected_objects[0]
    plane_obj.rotation_euler.rotate_axis(plane['axis'], 1.5708)
    plane_obj.location[0] = obj.dimensions[0] * (plane['axis'] == 'X') * plane['direction']
    plane_obj.location[1] = obj.dimensions[1] * (plane['axis'] == 'Y') * plane['direction']
    plane_obj.location[2] = obj.dimensions[2] * (plane['axis'] == 'Z') * plane['direction'] * plane['position']
    
    # Use the boolean modifier to fold the object along the plane
    mod = obj.modifiers.new(name="fold", type='BOOLEAN')
    mod.object = plane_obj
    mod.operation = 'DIFFERENCE'
    bpy.ops.object.modifier_apply(modifier=mod.name)
    
    # Delete the plane object
    bpy.ops.object.select_all(action='DESELECT')
    plane_obj.select_set(True)
    bpy.ops.object.delete(use_global=False)

# Export the folded object as glTF
bpy.ops.export_scene.gltf(filepath="path/to/folded_shirt.gltf", export_format='GLTF_EMBEDDED')
