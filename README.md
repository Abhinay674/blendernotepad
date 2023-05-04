import bpy

# Replace the path with the path to your new glTF file
new_shirt_path = "/path/to/your/new/shirt.gltf"

# Import the new shirt
bpy.ops.import_scene.gltf(filepath=new_shirt_path)

# Get a reference to the new shirt object
new_shirt = bpy.context.selected_objects[0]

# Get a reference to the hanger object
hanger = bpy.data.objects["hanger"]

# Set the new shirt as a child of the hanger
new_shirt.parent = hanger

# Position the new shirt on the hanger
new_shirt.location = (0.0, 0.0, 0.0)
new_shirt.rotation_euler = (0.0, 0.0, 0.0)
new_shirt.scale = (1.0, 1.0, 1.0)

# Remove the old shirt object
old_shirt = bpy.data.objects["shirt"]
bpy.data.objects.remove(old_shirt, do_unlink=True)
