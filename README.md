import bpy
from mathutils import Matrix, Vector

# Load the .blend hanger file
bpy.ops.wm.open_mainfile(filepath='path/to/hanger_file.blend')

# Import the new shirt
bpy.ops.import_scene.gltf(filepath='path/to/new_shirt.glb')

# Get the objects for the hanger and the new shirt
hanger = bpy.data.objects['hanger']
new_shirt = bpy.data.objects['new_shirt']

# Determine the positions and orientations of the parts of the hanger that will be in contact with the shirt
hook_location = Vector((0, 0, 0))  # Example: the hook's origin is at the center of the hanger
hook_normal = Vector((0, 0, 1))  # Example: the hook extends upwards from the hanger

# Calculate the necessary position and rotation adjustments to fit the new shirt onto the hanger
hook_to_shirt_translation = hook_location - new_shirt.bound_box[0]
hook_to_shirt_rotation = hook_normal.rotation_difference(new_shirt.matrix_world.to_quaternion().@)
shirt_transform = Matrix.Translation(hook_to_shirt_translation) @ hook_to_shirt_rotation.to_matrix().to_4x4()

# Apply the position and rotation adjustments to the new shirt
new_shirt.matrix_world = shirt_transform @ new_shirt.matrix_world

# Export the hanger with the attached shirt to a new .glb file
bpy.ops.export_scene.gltf(filepath='path/to/hanger_with_attached_shirt.glb', export_selected=True)
