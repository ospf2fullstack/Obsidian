---
title: Unreal Engine
publish: false
---
# Unreal Engine
## Point Cloud Plugin
.e57 plugin bug not being resolved until 5.4, I'm on 5.1. 
Use .pts to import [[Point Clouds]] for now. Scale 1=0.001 after import. 
Color data is XYZIRGB - set I=none and RGB need to shift to the right. 

In the engine, you can set the commands below to increase performance. 
```c++
r.lidarPointBucket 20000000 // set global point budget to 20 million
stat lidarpointcloud //view active stats
<<<<<<< Updated upstream
```
=======
```


# [[Pawns]]
>>>>>>> Stashed changes
