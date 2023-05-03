# Get the hanger object
hanger_obj = None
for obj in bpy.context.scene.objects:
    if obj.name == "ZZZZZZZZZZZ":
        hanger_obj = obj
        break

if hanger_obj is not None:
    # Set the shirt object as a child of the hanger object
    shirt_obj.parent = hanger_obj

    # Set the orientation matrix of the shirt object
    orientation_matrix = bpy.data.objects["ZZZZZZZZZZZ"].matrix_world.inverted() @ shirt_obj.matrix_world
    shirt_obj.rotation_euler = orientation_matrix.to_euler()

    # Transform the shirt object
    shirt_obj.scale *= 0.008
    shirt_obj.rotation_euler.rotate_axis("X", 1.5708)
    shirt_obj.scale.y *= 0.5
    shirt_obj.location.z -= 10.1

    # Export the GLTF file
    file_loc3 = hanger_file_name[0:-5] + '_hanger'
    file_loc2 = file_loc3
    bpy.ops.export_scene.gltf(filepath=file_loc2, export_format='GLTF_EMBEDDED')
else:
    print("Hanger object not found in scene.")
