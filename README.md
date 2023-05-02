import bpy
import os
import sys
import mathutils

# Set input and output paths
input_path = "path/to/input/file.gltf"
output_path = "path/to/output/file.gltf"

# Set up scene and camera
scene = bpy.context.scene
cam = scene.camera

# Import GLTF file
bpy.ops.import_scene.gltf(filepath=input_path)

# Select shirt object and add cloth simulation
shirt = bpy.data.objects['shirt']
bpy.context.view_layer.objects.active = shirt
bpy.ops.object.modifier_add(type='CLOTH')
cloth = shirt.modifiers['Cloth']
cloth.settings.vertex_group_mass = 'Group'
cloth.settings.quality = 10
cloth.settings.time_scale = 1
cloth.settings.tension = 40
cloth.settings.shear_force = 0.5
cloth.settings.use_pressure = True
cloth.settings.uniform_pressure_force = 0.02
bpy.ops.object.mode_set(mode='EDIT')
bpy.ops.mesh.select_all(action='SELECT')
bpy.ops.mesh.normals_make_consistent(inside=False)
bpy.ops.object.mode_set(mode='OBJECT')

# Add collision objects for folding
left_plane = bpy.data.objects.new('left_plane', None)
right_plane = bpy.data.objects.new('right_plane', None)
bottom_plane = bpy.data.objects.new('bottom_plane', None)
top_plane = bpy.data.objects.new('top_plane', None)
for obj in [left_plane, right_plane, bottom_plane, top_plane]:
    scene.collection.objects.link(obj)
    obj.empty_display_size = 0.5
    obj.empty_display_type = 'PLAIN_AXES'
    obj.hide_render = True
    obj.hide_viewport = True

# Set up folding planes
left_plane.rotation_euler = mathutils.Euler((0, math.pi/2, 0), 'XYZ')
left_plane.location = shirt.bound_box[0]
right_plane.rotation_euler = mathutils.Euler((0, -math.pi/2, 0), 'XYZ')
right_plane.location = shirt.bound_box[6]
bottom_plane.rotation_euler = mathutils.Euler((math.pi/2, 0, 0), 'XYZ')
bottom_plane.location = shirt.bound_box[0]
top_plane.rotation_euler = mathutils.Euler((-math.pi/2, 0, 0), 'XYZ')
top_plane.location = shirt.bound_box[4]

# Run folding animation
frame_count = 30
for frame in range(frame_count):
    scene.frame_set(frame)
    cloth.settings.surface_distance = 0.01 * (frame_count - frame)
    cloth.settings.bending_stiffness = 0.05 * (frame_count - frame)
    left_plane.location += mathutils.Vector((0, 0.02, 0))
    right_plane.location += mathutils.Vector((0, -0.02, 0))
    bottom_plane.location += mathutils.Vector((0.02, 0, 0))
    top_plane.location += mathutils.Vector((-0.02, 0, 0))
    bpy.ops.object.mode_set(mode='OBJECT')

# Export folded shirt as GLTF
bpy.ops.export_scene.gltf(filepath=output_path, export_selected=True, export_format='GLTF_SEPARATE', export_apply=True)

# Remove objects and materials
bpy.ops.object.select_all(action='SELECT')
bpy.ops.object.delete()
bpy.data.materials.remove(bpy.data.materials['Material'])
