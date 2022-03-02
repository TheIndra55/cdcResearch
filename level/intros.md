# Intros

Intros are instances which are born when the level is loaded, intros can also be markers. Each intro has a position, rotation and intro id whereas the intro id is used by the [Event](event) system to find the object.

The intro list is referenced to from the `Terrain` and `Level` struct, loop trough `numIntros` at the padding of `introList` and you will get all intros.
```cpp
struct Level {
	...
	int numIntros;
	Intro* introList;
```

**Note** If you change or add new intros make sure to modify the objectNameList too else they won’t load.

## Markers

Intros are also used as markers, a marker will have `objectID` -6 and won't birth an instance.