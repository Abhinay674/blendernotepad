import bpy
import os

# Set the path to the input glTF file
input_file = "path/to/input.gltf"

# Set the path to the output glTF file
output_file = "path/to/output.gltf"

# Load the glTF file
bpy.ops.import_scene.gltf(filepath=input_file)

# Find the imported object (assume there is only one)
imported_obj = None
for obj in bpy.context.scene.objects:
    if obj.type == 'MESH':
        imported_obj = obj
        break

if imported_obj is None:
    raise Exception("No mesh object found in the imported glTF file.")

# Create a cloth simulation for the imported mesh
cloth = imported_obj.modifiers.new('Cloth', 'CLOTH')
cloth.settings.vertex_group_mass = 'Group'
cloth.settings.quality = 5
cloth.settings.time_scale = 0.1
cloth.settings.shear = 0.5
cloth.settings.bending = 0.5
cloth.settings.use_pressure = True

# Set the shirt's properties
imported_obj['Shirt Type'] = 'Folded'

# Add a plane as the collision object
collider = bpy.data.objects.new('Plane', bpy.data.meshes.new('Plane'))
collider.location = (0, 0, 0)
collider.scale = (10, 10, 10)
bpy.context.scene.collection.objects.link(collider)

# Set the collision object's properties
collider['Collision Type'] = 'Plane'
collider.hide_render = True
collider.hide_viewport = True

# Set up the cloth simulation
bpy.context.scene.frame_set(0)
bpy.ops.object.mode_set(mode='EDIT')
bpy.ops.mesh.select_all(action='SELECT')
bpy.ops.transform.translate(value=(0, 0, 1))
bpy.ops.object.mode_set(mode='OBJECT')

bpy.context.scene.rigidbody_world.point_cache.frame_start = 0
bpy.context.scene.rigidbody_world.point_cache.frame_end = 100
bpy.context.scene.rigidbody_world.point_cache.use_disk_cache = True
bpy.context.scene.rigidbody_world.point_cache.directory = "/tmp/blender_cache/"

# Run the simulation
bpy.ops.ptcache.bake_all(bake=True)

# Export the folded shirt as a new glTF file
bpy.ops.export_scene.gltf(filepath=output_file, export_format='GLTF_SEPARATE')
