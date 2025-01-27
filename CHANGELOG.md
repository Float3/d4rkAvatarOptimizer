## Next Version
### Features
* Add option to merge simple toggles in the fx layer into one big direct blend tree.

## v1.10
### Features
* Add debug view to show all animated material property paths.
* Several small improvements to the generated shaders and mesh layouts reducing the amount of VRAM used by the optimized avatar.

### Changes
* Show original mesh paths in the `_IsActiveMesh` properties of merged materials.
* Don't inject animated property arrays for merged material properties where none of the original meshes had that property animated.
* Calculate min and max mesh id for each merged material and only add animated property arrays for that range.
* Only add the animated property based on the used mesh indices for the merged material.
* Interleave animated property arrays in cbuffer to reduce the required cbuffer size.
* Pack original meshID & materialID into uv0.z instead of having them separate in uv0.z & uv0.w. Miniscule VRAM savings. [(more)](https://github.com/d4rkc0d3r/d4rkAvatarOptimizer/issues/27)
* Call `Mesh.Optimize()` on the merged mesh to reduce the amount of memory it takes up. [(more)](https://github.com/d4rkc0d3r/d4rkAvatarOptimizer/issues/27)

### Bug Fixes
* Delete shader properties that start with _ShaderOptimizer from the optimized shader. This is to prevent Kaj shader optimizer from trying to optimize the shader again.
* Fix `RemoveIllegalComponents()` being broken when the sdk was imported via VCC.
* Change signaling value to NaN. This fixes animated properties interpolating the signal value weirdly.
* Create optimized swap materials after merging materials instead of before so that Texture2DArray and animated properties are correctly set.
* Deduplicate optimized swap materials with the optimized material that is already in the mesh renderer.
* Fix vertex shader not passing along mesh id to the geometry shader if the mesh id is disabled.

## v1.9
### Features
* Multiple performance improvements that reduce the time it takes to optimize an avatar by a factor of around 2x.

### Changes
* Instead of doing a `AssetDatabase.Refresh()` to import the optimized shaders it now uses `ImportAsset()` inside a `StartAssetEditing()` and `StopAssetEditing()` block. This saves a tiny bit of time.
* Replaced two heavily used regex with string and char operations. This saves a bit of time.
* Moved most of the generated shader code into include files. This saves a lot of time when importing the shaders.
* Strip `[Drawer]` attributes from the generated shader code. This saves a significant amount of time when assigning the shader to a material.

### Bug Fixes
* Fix that the optimizer would try to merge textures with different mip count.
* Fix crash when the avatar has a component with a missing script on it.

## v1.8
### Features
* Add info text if any normal maps are found that are not using BC5 compression.
* Add button to automatically convert all normal maps to BC5 compression.
* Add Texture Compression Analyzer under the `Tools/d4rkpl4y3r/Texture Compression Analyzer` menu.

### Changes
* The optimized animator controllers now have `(OptimizedCopy)` appended to their name.
* Add Limitations section to the readme to clarify that avatars might look broken if shaders are disabled. [(more)](https://github.com/d4rkc0d3r/d4rkAvatarOptimizer/issues/17)
* Slight blend shape download size reduction by setting delta values to 0 if they are below 1e-8.

### Bug Fixes
* Fix parents of animated transforms getting deleted. [(more)](https://github.com/d4rkc0d3r/d4rkAvatarOptimizer/issues/14)
* Always enable SkinnedMeshRenderer component that gets other meshes merged into it. [(more)](https://github.com/d4rkc0d3r/d4rkAvatarOptimizer/issues/15)
* Ignore Renderers that have no materials. [(more)](https://github.com/d4rkc0d3r/d4rkAvatarOptimizer/issues/19)
* Only add `#define KEYWORD` to passes that have a corresponding `#pragma shader_feature` in the pass. [(more)](https://github.com/d4rkc0d3r/d4rkAvatarOptimizer/issues/18)
* Fixed that multiple vert/geometry/fragment shader declarations would cause the optimizer to only use the last one.
* Add `Texture2D.GetDimensions(uint mipLevel, out float width, out float height, out float numberOfLevels)` and the `uint` variant overloads to the merged texture wrapper class. [(more)](https://github.com/d4rkc0d3r/d4rkAvatarOptimizer/issues/20)
* Fix assumed default vertex color to be white instead of black.
* Ignore Renderers that have no mesh. [(more)](https://github.com/d4rkc0d3r/d4rkAvatarOptimizer/issues/22)
* Vector4 properties now use G formatting instead of the default unity F1 formatting. [(more)](https://github.com/d4rkc0d3r/d4rkAvatarOptimizer/issues/21)

## v1.7
### Bug Fixes
* Fix optimizer not working with VRCSDK3-AVATAR-2022.10.12.21.07 SDK onwards. [(more)](https://github.com/d4rkc0d3r/d4rkAvatarOptimizer/issues/12)

## v1.6
### Features
* Optimizer now shows a progress bar dialog when optimizing an avatar.
* Add info box if build target is Android to tell users that the optimizer doesn't support Quest avatars.
* Add validation check that checks if the optimizer got added to an optimized copy.
* Add validation error if the amount of base playable layers isn't 5.

### Bug fixes
* Fix extra out parameters in the fragment shader not getting added to the function declaration. [(more)](https://github.com/d4rkc0d3r/d4rkAvatarOptimizer/issues/10)
* Add ParserException if any parameter in vertex or fragment can't be parsed.
* Fix parsing of template types to permit spaces between the type and the template brackets.
* Fix new lines in string literals not being the environment's new line character. This caused shader warning spam on poi shader.
* Fix default texel size to 4 instead of 8. This matches the sizes of the default textures as checked with nvidia nsight.
* Fix non float params not working with "Keep Material Animations Separate" option.

## v1.5
### Bug fixes
* Fix crash when an EditorOnly tagged game object was in the exclusions list.
* Fix non Texture2D textures that are the same reporting that they can't be combined.
* Fix non Texture2D textures trying to combine with `null`.
* Fix crash when vertex shader has no input struct.
* Fix geometry shader input type to also allow for point & line.
* Fix nested BlendTrees not getting iterated through to fix animation paths.
* Move the optimized copy to the same scene as the original.
* Replace VFACE semantic with SV_IsFrontFace.
* Force include TEXCOORD0 semantic in the vertex shader input struct. This fixes cases where the sematic only exists in some shader variants.
* Don't delete transforms that are moved by animations.
* Don't delete parent transforms of Constraints.
* Deep copy blend trees when fixing animation paths.

## v1.4
### Features
* Add info box educating about increased poly count after optimization if some renderer has more material slots than sub meshes.

### Bug fixes
* Shader optimizer skips parsed shaders that are null or parsed incorrectly.
* Fix floats getting written with system localization instead of invariant culture.
* Fix crash when an animation has a material swap that doesn't point to a transform on the avatar. Fix for: [Null pointer error on Create Optimized Copy](https://github.com/d4rkc0d3r/d4rkAvatarOptimizer/issues/7)
* Fix bug where the merge mesh logic would ignore the exclusion list when merging into an excluded mesh.
* Fix merge perf rank preview pre optimization counting EditorOnly meshes & materials.
* Fix merge perf rank preview not taking into account particle system material slots.
* Fix merge perf rank preview counting the "hidden" extra material slots if they didn't get baked into a mesh.
* Fix bug with animated properties that ended in numbers.
* Fix that debug info for all game objects with toggles would list the always disabled game objects instead.
* Fix frag inject code not running if there were no array properties or mesh toggles. This caused the code to preserve texture samplers not to be injected. Fix for: [Shader issue?](https://github.com/d4rkc0d3r/d4rkAvatarOptimizer/issues/8)
* Write active mesh toggle state to the `_IsActiveMesh` material properties. Also base them on the Renderer enabled state instead of just the game object active state.

### Debug Info
* Catch exception in the shader optimizer to show which shader is causing the exception.

## v1.3
### Features
* Added perf rank change indicators to the Merge Preview foldout.

### Bug fixes
* Fixed that Color properties without hdr tag wrote the raw sRGB value as a linear value into the shader.
* Some meshes can have `null` bones. Use the root bone instead. Fix for: [NullReferenceException: Object reference not set to an instance of an object.](https://github.com/d4rkc0d3r/d4rkAvatarOptimizer/issues/6)

## v1.2
### Features
* Show warning if there are textures that are crunch compressed.
* Add list of all crunch compressed textures to the debug info section.
* Always skip variants `DYNAMICLIGHTMAP_ON`, `LIGHTMAP_ON`, `LIGHTMAP_SHADOW_MIXING`, `DIRLIGHTMAP_COMBINED`, and `SHADOWS_SHADOWMASK` since they are unused on avatars.

### Bug fixes
* Fixed the delete unused gameobjects function not respecting the exclusions properly.
* Don't merge textures that are crunch compressed. Fix for: [Material merging results in corrupted material/textures](https://github.com/d4rkc0d3r/d4rkAvatarOptimizer/issues/5)
* Inline replace the `UNITY_POSITION` macro. Fix for: [Material merging results in corrupted material/textures](https://github.com/d4rkc0d3r/d4rkAvatarOptimizer/issues/5)
* Change default "bump" texture value to the correct value of `float4(0.5, 0.5, 1, 1)` instead of the incorrect 0.5 alpha that the unity doc says. [I noticed that if I merge materials where one of them has a normal map and the others do not, I can run into broken normals](https://github.com/d4rkc0d3r/d4rkAvatarOptimizer/issues/5#issuecomment-1220827519)
* Added `__Baked` property check for locked in shaders. This is used by silent crosstone for shader locking.
* Fix uv0 end swizzle not being applied to the original vertex shader copy.

### Debug info
* Add original shader name to the generated shader as a comment.

## v1.1
### Features
* Added exclusion Transform list. Anything that is under the hierarchy of the Transforms in the exclusion list will not be touched by the optimizer.  
  Implements suggestion: [if we could get a way to exclude certain things that would fix it](https://twitter.com/JettsdVRC/status/1559692330965372930)
* Show warning if any materials are locked in.
* Add a list of all locked in materials in the debug info foldout.
### Bug fixes
* Fixed a bug when "Write Properties as Static Values" was disabled with "Merge Same Dimension Textures" enabled and a material had to sample from only one arrayIndex.
* Count parsed shaders that have 0 lines as unparsable.  
  Fix for: [Out of range error cancelling optimisation](https://github.com/d4rkc0d3r/d4rkAvatarOptimizer/issues/4)
