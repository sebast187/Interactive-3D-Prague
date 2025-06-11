Here is a list of the incorrect theories we tested and why each one was wrong, so you know what paths not to take.

1. The Theory: Z-Fighting
What I thought the problem was: The bottom of the 3D buildings and the top of the map tiles were at the exact same height (Y=0), causing the graphics card to struggle with which one to draw, making the map tiles disappear.
The Attempted Fix: I added polygonOffset to the map tile's material. This is a standard technique to tell the renderer to "push" one surface slightly behind another to resolve such conflicts.
Why it was wrong: This wasn't the issue. The terrain wasn't just conflicting with buildings; it was disappearing entirely, even where there were no buildings. Your feedback that the "movement was wrong" was the key clue that the problem was related to geometry and position, not just a surface rendering conflict.
2. The Theory: Incorrect Vertex Axis
What I thought the problem was: Your code modifies the Z coordinate of the plane's vertices (positions.setZ(...)) and then rotates the plane. I theorized that this rotation was mapping the height to the wrong world axis, creating a deep chasm.
The Attempted Fix: I changed the code to modify the Y coordinate instead (positions.setY(...)).
Why it was wrong: While this correctly oriented the direction of the height (as you noted, the movement felt correct), it didn't solve the core invisibility problem. It proved the height was being applied along the right axis after rotation, but it didn't explain why the whole object was still invisible.
3. The Theory: Face Culling / Flipped Normals
What I thought the problem was: After the vertex manipulation, the calculated "front" faces of the terrain were pointing downwards, away from the camera. The renderer, trying to be efficient, was culling (not drawing) the "back" faces you were looking at.
The Attempted Fix: I added side: THREE.DoubleSide to the plane's material. This tells the renderer to ignore which side is the front and just draw everything.
Why it was wrong: This is a common fix for procedural geometry, but it didn't solve the problem here. This was a strong indicator that the geometry wasn't just flipped; it wasn't in the camera's view at all.
4. The Theory: Flawed Order of Operations
What I thought the problem was: The order was wrong. I thought you had to rotate the geometry first, then apply the height modification, and then create the Mesh without rotation.
The Attempted Fix: I restructured the createTile function significantly, using planeGeom.rotateX(...) before the loop.
Why it was wrong: This was an over-complication of the problem. Your original logic of rotating the Mesh object is perfectly valid. My change was not only unnecessary but, as the error console showed, I introduced new bugs into the code that broke it completely.
