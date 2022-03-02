# Object list

The object list file is used to lookup object names from object ids, it's named `objectlist.txt` and located in game name folder.

## Format

The first line contains a single decimal number with the number of objects in the file, all lines after are comma separated `id,name`.

The last line must always end with a line break else the game will crash.

## Example

```
949
1,generalbank
2,globalsoundinfo
3,lara
4,particle
5,uigeneral
6,handgun_rbweapon
7,watervolume
8,torso_boss
9,doppelganger
10,doppelgun
```