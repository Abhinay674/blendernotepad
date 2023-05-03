Dlendblendfile\Folding_Template-Hanging.blend
D:\GLTFVERTICAL\BQ7270_KiksWOL_front-black.gltf
Data are loaded, start creating Blender stuff
glTF import finished in 0.18s
Error: Python: Traceback (most recent call last):
  File "\Text", line 27, in <module>
NameError: name 'hanger_obj' is not defined




import bpy
import sys
from bpy_extras.io_utils import ImportHelper
from bpy.types import Operator
from bpy.props import StringProperty
from bpy.utils import register_class

#argv = sys.argv
#print("Prachi args",argv)

hanger_file_name = 'D:\blendblendfile\Folding_Template-Hanging.blend'
print(hanger_file_name);
gltf_file_name = 'D:\GLTFVERTICAL\BQ7270_KiksWOL_front-black.gltf'
print(gltf_file_name);

# Import the GLTF file and get the shirt object
bpy.ops.import_scene.gltf(filepath=gltf_file_name)
shirt_obj = bpy.data.objects["BQ7270.002"]

# Get the hanger object
for obj in bpy.context.scene.objects:
    if obj.name == "ZZZZZZZZZZZ":
        hanger_obj = obj
        break

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
file_loc3 = hanger_file_name[0:-5]+'_hanger'
file_loc2 = file_loc3
bpy.ops.export_scene.gltf(filepath=file_loc2, export_format='GLTF_EMBEDDED')
