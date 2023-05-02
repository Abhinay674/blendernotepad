import bpy

# Replace with the filepath of the glTF file to import
file_loc = "/path/to/shirt.gltf"

# Import glTF file
bpy.ops.import_scene.gltf(filepath=file_loc)

# Select the imported object
obj = bpy.context.selected_objects[0]
obj.select_set(True)
bpy.context.view_layer.objects.active = obj

# Scale down the object
bpy.ops.transform.resize(value=(0.01, 0.01, 0.01))
bpy.ops.transform.resize(value=(1, 1, 0.1), constraint_axis=(False, False, True))

# Apply fold modifier to create desired folds
fold_modifier = obj.modifiers.new(name="Fold", type='SIMPLE_DEFORM')
fold_modifier.deform_method = 'BEND'
fold_modifier.deform_axis = 'X'
fold_modifier.factor = -0.5
fold_modifier.show_in_editmode = True

# Export the folded shirt glTF file
file_loc2 = "/path/to/folded_shirt.gltf"
bpy.ops.export_scene.gltf(filepath=file_loc2, export_format='GLTF_EMBEDDED')
