# PS2-SAS-icon-guide
A guide to create icons for SAS-compliant PS2 apps. Includes assets and tools.

![PS2 browser](images/ps2_browser.jpg?raw=true "PS2 browser")

## Software used
This repo contains tools made by the ps2 community over the decades. Most are quite old and only work on windows.  
Beside these very specific tools, you will need [Blender](https://www.blender.org/download/) and [Gimp](https://www.gimp.org/downloads/) (or Photoshop if you prefer).

## Icon basics
In order to create a PS2 app that is SAS-compliant, the app folder must contains the files required for the PS2 Browser to display an icon. Without such files, the PS2 Browser will just show a blue cube with "Corrupted Data" as title.

![Corrupted cube](images/ps2_browser_corrupted.jpg?raw=true "Corrupted cube")

In addition, the icon must include one or two indicators as described [here](https://ps2wiki.github.io/documentation/homebrew/PS2-App-System/SAS/indicators-and-warnings/indicators-and-warnings) ([archive](https://web.archive.org/web/20250629140241/https://ps2wiki.github.io/documentation/homebrew/PS2-App-System/SAS/indicators-and-warnings/indicators-and-warnings)).  
The files required to make an icon are:
* `icon.sys` contains various information about the name of the save, the filenames of the models, the light sources, etc. It is described in details [here](https://babyno.top/en/posts/2023/10/parsing-ps2-3d-icon/#02-parsing-iconsys) ([archive](https://web.archive.org/web/20250418090328/https://babyno.top/en/posts/2023/10/parsing-ps2-3d-icon/#02-parsing-iconsys)).
* The base icon, usually named `list.icn`, contains the 3d model and its texture displayed by default in the PS2 Browser
* The copy icon, usually named `copy.icn`, contains the 3d model and its texture displayed by in the PS2 Browser when copying the app
![Copy app](images/ps2_browser_copy.jpg?raw=true "Copy app")
* The delete icon, usually named `del.icn`, contains the 3d model and its texture displayed by in the PS2 Browser when deleting the app
![Delete app](images/ps2_browser_del.jpg?raw=true "Delete app")

## Creating `icon.sys`
[iconsys_builder.exe](tools\ps2iconsys\iconsys_builder.exe) is a command line tool that lets you create a `icon.sys`. With the same tool, you can also edit an existing `icon.sys`.  
`.\iconsys_builder.exe -h` shows the help.  
`.\iconsys_builder.exe -o icon.sys --set-title "Sample App" --set-icon list.icn --set-copy-icon copy.icn --set-delete-icon del.icn` will create a basic icon.sys.

Title is what appear as the save name (in yellow) in the PS2 Browser.

## Making the 3D model
Grab [Placeholder_v1.0-2.blend](assets/Placeholder_v1.0-2.blend) and [TEXTURErev2.png](assets/TEXTURErev2.png). This blender project contains all the indicator models and their textures, and it contains a picture frame.

![Blender template](images/blender.png?raw=true "Blender template")

You can use the picture frame to position your own 3D model into the right position. You can also hide the indicators that are not needed for your app. Bear in mind that the whole model (main model + indicators) **should have less than 700 triangles**, ideally less than 600, or it will not be displayed by the PS2 Browser.

![Add Main Model](images/add_main_model.png?raw=true "Add Main Model")

The PS2 icon need to have a single square texture file, so you can edit `TEXTURErev2.png` to add the texture you need for your own model and remove the texture of unneeded indicators. Keep in mind that the target texture is a bmp file with a dimension of 128x128.

![Adjust Texture](images/adjust_texture.png?raw=true "Adjust Texture")

Once you are done hiding the indicators, placing the main model, and tinkering with the texture, you can export the finished model. Click `File -> Export -> Wavefront (.obj)` and un-check `Material`. The Material option creates a .mtl file which causes potential issues in the next steps.

![Blender Export](images/blender_export.png?raw=true "Blender Export")

## Prepare the texture
The delete and copy icon can use the same model, but the texture needs to be modified to make the memory card indicator red and green.

![Icon Set](images/icon_set.png?raw=true "Icon Set")

The easiest way to do this is to open the texture in Gimp, and add a 50% opacity overlay on top the memory card texure. For the delete texture, fill the overlay with #B7140F. For the copy texture, fill the overlay with #16B70F.

![Indicator Overlay](images/indicator_overlay.png?raw=true "Indicator Overlay")

Take the textures you prepared, resize them to 128x128, and export them as a 24bits (R8 G8 B8) bitmap.

![Gimp Export](images/gimp_export.png?raw=true "Gimp Export")

You should now have 3 128x128 bitmap texture. A normal one, the red one, and the green one

![Texture Set](images/texture_set.png?raw=true "Texture Set")

## Convert the .obj and the texture into a PS2 icon
### Install milkshape and the PS2 plugin
To convert to the PS2 format, use Milkshape 3D. It is available [here](http://milkshape3d.com/ms3d/download.html). The website is unsecure, so the installer is also available [here](tools/ms3d184setup.exe).

You also need to install a milkshape 3D plugin to enable exporting PS2 icons. To do so, grab the 2 .dll ([msPs2Exporter.dll](tools/ps2icon-plugin/msPs2Exporter.dll) and [msPs2Importer.dll](tools/ps2icon-plugin/msPs2Importer.dll)), and copy them at the root of the Milkshape install folder (by default `C:\Program Files (x86)\MilkShape 3D 1.8.4`).

### Convert the .ojb
/!\ Make sure there is no .mtl files in your folder.

Hit `File -> Import -> Wavefront OBJ...` to import your fresh .obj file. You should see your model appear without any texture.

On the right side of the milkshape window, open the `Materials` tab. Here, hit the `New` button to create a new material. Then, click on the first `<none>` button and select the 128x128 texture you prepared.

![Milkshape](images/milkshape.png?raw=true "Milkshape")

The texture will not appear on the model, but this is fine. Now hit `File -> Export -> PS2 Icon(*.icn)` and export the `.icn` file.

You need to repeat the last 2 steps (set material texture file and export) for the other 2 textures.

## Finishing touches
You should now have icon.sys, list.icn, del.icn, and copy.icn.

You can use [ps2_iconsys_viewer.exe](tools/ps2_iconsys_viewer.exe) to open icon.sys and see the number of triangles. Make sure it's under 700, or it's very likely to not display.

![ps2_iconsys_viewer](images/ps2_iconsys_viewer.png?raw=true "ps2_iconsys_viewer")

You can also open [PS2IconViewer_v1.1.0.exe](tools/PS2IconViewer_v1.1.0.exe) and drag and drop the icon.sys in the window. This should display the 3 icons you just made.

![ps2iconviewer](images/ps2iconviewer.png?raw=true "ps2iconviewer")

Finally, you can open [ps2save-builder.exe](tools/Ps2_Save_Builder_0.8x/ps2save-builder.exe) and drag an drop icon.sys, list.icn, del.icn, and copy.icn. You can then right click each file to open it. If you edit icon.sys, you can change the lighting of the icon. Make sure you set the background transparency appropriately, or the lightining change will not be visible.

![ps2save-builder](images/ps2save-builder.png?raw=true "ps2save-builder")

## Example
In the `example/` folder, you can find the [raw model](example/Dualshock_(PS1).fbx), the blender project, the exported .obj, the 3 textures, the icon.sys, and the 3 .icn. This specific example is the actual icon used by the PadTest app.  
["Dualshock (PS1)" by YoukaiDrawing](https://skfb.ly/oItXN) is licensed under [Creative Commons Attribution](http://creativecommons.org/licenses/by/4.0/). 

## Common issues
_PS2 Browser shows a blue cube instead of my icon._  
This is likely because the icn file contains more than 700 triangles or is larger than 65KB. Try to reduce the triangle count in blender and re-export.

_Milkshape fails to export the icon._  
Make sure there is no .mtl file with the same name of the .obj.  
Make sure that there is only one material, with one texture.  
Make sure that there is only one mesh group.

_The icon viewer tools are not showing my icon._  
Double check that the paths in icon.sys point to the right .icn filename.

## Credits
~in no particular order~

**Ripto** for sharing his treasure trove of resources, especially the main blender project.  
**Ticky** for their tools to manipulate saves [ticky/ps2iconsys](https://github.com/ticky/ps2iconsys).  
**Vector** for *ps2save-builder.exe* and the milkshape plugin.  
**H. Yoshida** for *PS2IconViewer_v1.1.0.exe*.  
**YoukaiDrawing** for their nice [Dualshock model](https://skfb.ly/oItXN).  
The fine folks from the PS2 Scene discord server for their patient guidance.  
