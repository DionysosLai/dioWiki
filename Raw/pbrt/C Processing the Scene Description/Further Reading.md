---
title: "Further Reading"
source: "https://pbr-book.org/4ed/Processing_the_Scene_Description/Further_Reading"
author:
published:
created: 2026-04-11
description:
tags:
  - "clippings"
---
pbrt ’s scene file format is custom, which has allowed us to tailor it to present all the system’s capabilities, though it makes it more challenging to import scenes from other systems, requiring a conversion step. (See the pbrt website for links to a number of such converters.)

There has been little standardization in these file formats; many 3D graphics file formats have been developed, in part due to the needs of graphics systems changing over time and in part due to lack of standardization on material and texture models. In addition to its own text format, pbrt does support the PLY format for specifying polygon meshes, which was originally developed by Greg Turk in the 1990s. PLY provides both text and binary encodings; the latter can be parsed fairly efficiently. Pixar’s *RenderMan* interface ([Upstill 1989](#cite:Upsill89); [Apodaca and Gritz 2000](#cite:Apodaca00)) saw some adoption in past decades, and the ambitiously named *Universal Scene Description* (USD) format is currently widely used in film production ([Pixar Animation Studios 2020](#cite:Pixar20)).

### References

1. Apodaca, A. A., and L. Gritz. 2000. *Advanced RenderMan: Creating CGI for Motion Pictures.* San Francisco: Morgan Kaufmann.
2. Pixar Animation Studios. 2020. Universal Scene Description. [https://graphics.pixar.com/usd/docs/index.html](https://graphics.pixar.com/usd/docs/index.html).
3. Upstill, S. 1989. *The RenderMan Companion*. Reading, Massachusetts: Addison-Wesley.
4. Walt Disney Animation Studios. 2018. *Moana Island Scene*. [https://www.disneyanimation.com/resources/moana-island-scene](https://www.disneyanimation.com/resources/moana-island-scene).

---
## 🔗 Related

**来源**: [[pbrt]]
**作者**: [[Matt Pharr]], [[Wenzel Jakob]], [[Greg Humphreys]]

**同章节**:
- [[C Processing the Scene Description]]
- [[C.1 Tokenizing and Parsing]]
- [[C.2 Managing the Scene Description]]
- [[C.3 BasicScene and Final Object Creation]]
- [[C.4 Adding New Object Implementations]]
