blender -b D:\blendblendfile\Folding_Template-Hanging.blend -P D:\blendscript\Hanging_Code.txt D:\GLTFVERTICAL\Mohith.obj

#Code to import asset 

import bpy
import sys
from bpy_extras.io_utils import ImportHelper
from bpy.types import Operator
from bpy.props import StringProperty
from bpy.utils import register_class

argv = sys.argv
print("Prachi args",argv)

hanger_file_name=argv[5]

meshname = ''


#file_loc ='C:/IXRVP/Storage/3DAssets\\'+hanger_file_name
file_loc =hanger_file_name
print("aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa",file_loc)
print(hanger_file_name)
#file_loc3 = 'C:/IXRVP/Storage/3DAssets\\'+hanger_file_name[0:-5]+'_hanger'
file_loc3 = hanger_file_name[0:-5]+'_hanger'



#Replace the filepath with the filepath of the asset stored in the server

bpy.ops.import_scene.gltf(filepath=file_loc)

#Code to select the imported file (Blender by default donot select the imported GLTF file)

objects = bpy.context.scene.objects
for ob in bpy.data.objects:
    if(ob.type == "MESH"):
        meshname = ob.name
        break;
    break;

bpy.data.objects[meshname].select_set(True)
bpy.context.view_layer.objects.active = bpy.data.objects[meshname]

#Code to position the asset on the hanger

bpy.ops.transform.resize(value=(0.008, 0.008, 0.008), orient_type='GLOBAL', orient_matrix=((1, 0, 0), (0, 1, 0), (0, 0, 1)), orient_matrix_type='GLOBAL', mirror=True, use_proportional_edit=False, proportional_edit_falloff='SMOOTH', proportional_size=1, use_proportional_connected=False, use_proportional_projected=False)

bpy.ops.transform.rotate(value=1.5708, orient_axis='X', orient_type='GLOBAL', orient_matrix=((1, 0, 0), (0, 1, 0), (0, 0, 1)), orient_matrix_type='GLOBAL', constraint_axis=(True, False, False), mirror=True, use_proportional_edit=False, proportional_edit_falloff='SMOOTH', proportional_size=1, use_proportional_connected=False, use_proportional_projected=False)

bpy.ops.transform.resize(value=(1, 0.5, 1), orient_type='GLOBAL', orient_matrix=((1, 0, 0), (0, 1, 0), (0, 0, 1)), orient_matrix_type='GLOBAL', constraint_axis=(False, True, False), mirror=True, use_proportional_edit=False, proportional_edit_falloff='SMOOTH', proportional_size=1, use_proportional_connected=False, use_proportional_projected=False)

bpy.ops.transform.translate(value=(0, 0, -10.1), orient_type='GLOBAL', orient_matrix=((1, 0, 0), (0, 1, 0), (0, 0, 1)), orient_matrix_type='GLOBAL', constraint_axis=(False, False, True), mirror=True, use_proportional_edit=False, proportional_edit_falloff='SMOOTH', proportional_size=1, use_proportional_connected=False, use_proportional_projected=False)

#Code to export the gltf file

#Replace the filepath with the location of the exported file
file_loc2 = file_loc3
bpy.ops.export_scene.gltf(filepath=file_loc2, export_format ='GLTF_EMBEDDED',)
