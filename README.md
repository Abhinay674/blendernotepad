import bpy

# Specify the input and output file paths
input_path = "/path/to/input/file.gltf"
output_path = "/path/to/output/file.gltf"

# Import the glTF file
bpy.ops.import_scene.gltf(filepath=input_path)

# Get a reference to the imported object
obj = bpy.context.selected_objects[0]

# Add a cloth simulation to the object
cloth = obj.modifiers.new('Cloth', 'CLOTH')
cloth.settings.vertex_group_mass = 'Group'
cloth.settings.quality = 5
cloth.settings.time_scale = 0.1
cloth.settings.shear = 0.5
cloth.settings.bending = 0.5
cloth.settings.use_pressure = True

# Set the shirt's properties
obj['Shirt Type'] = 'Folded'

# Add a plane as the collision object
collider = bpy.data.objects.new('Plane', bpy.data.meshes.new('Plane'))
collider.location = (0, 0, 0)
collider.scale = (10, 10, 10)
bpy.context.collection.objects.link(collider)

# Set the collision object's properties
collider['Collision Type'] = 'Plane'
collider.hide_render = True
collider.hide_viewport = True

# Set up the cloth simulation
scene = bpy.context.scene
scene.frame_set(0)
bpy.ops.object.mode_set(mode='EDIT')
bpy.ops.mesh.select_all(action='SELECT')
bpy.ops.transform.translate(value=(0, 0, 1))
bpy.ops.object.mode_set(mode='OBJECT')

scene.rigidbody_world.point_cache.frame_start = 0
scene.rigidbody_world.point_cache.frame_end = 100
scene.rigidbody_world.point_cache.use_disk_cache = True
scene.rigidbody_world.point_cache.directory = "/tmp/blender_cache/"

# Run the simulation
bpy.ops.ptcache.bake_all(bake=True)

# Export the modified mesh as a glTF file
bpy.ops.export_scene.gltf(filepath=output_path)
