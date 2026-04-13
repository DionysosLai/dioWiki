---
title: "Preface"
source: "Fundamentals of Computer Graphics, Fourth Edition"
author:
  - "Steve Marschner"
  - "Peter Shirley"
created: 2026-04-13
tags:
  - "clippings"
---
## Preface

Preface
This edition of Fundamentals of Computer Graphics includes substantial rewrites
of the chapters on textures and graphics hardware, as well as many corrections
throughout. The ﬁgures are now in color throughout the book.
The organization of the book remains substantially similar to the third edi-
tion. In our thinking, Chapters 2 through 8 c onstitute the “core core,” taking the
straight and narrow path through what is absolutely required for understanding
how images get onto the screen using the complementary approaches of ray trac-
ing and rasterization. Ray tracing is covered ﬁrst, since it is the simplest way to
generate images of 3D scenes, followed b y the mathematical machinery required
for the graphics pipeline, then the pipeline itself. After that, the “outer core” cov-
ers other topics that would commonly be included in an introductory class, such
as sampling theory, texture mapping, spatial data structures, and splines. Starting
with Chapter 15 is a number of contributed chapters, authored by contributors we
have chosen both for their expertise and for their clear way of expressing ideas.
As we have revised this book over the years, we have endeavored to retain
the informal, intuitive style of presentation that characterizes the earlier editions,
while at the same time improving consistency, precision, and completeness. We
hope the reader will ﬁnd the result is an appealing platform for a variety of courses
in computer graphics.
About the Cover
The cover image is from Tiger in the Water by J. W . Baker (brushed and air-
brushed acrylic on canvas, 16” by 20”, www.jwbart.com).
The subject of a tiger is a reference to a wonderful talk given by Alain Fournier
(1943–2000) at a workshop at Cornell University in 1998. His talk was an evoca-
tive verbal description of the movements of a tiger. He summarized his point:
Even though modelling and renderi ng in computer graphics have
been improved tremendously in the past 35 years, we are still not
at the point where we can model automatically a tiger swimming in
xi

xii Preface
the river in all its glorious details. By automatically I mean in a way
that does not need careful manual tweaking by an artist/expert.
The bad news is that we have still a long way to go.
The good news is that we have still a long way to go.
Online Resources
The website for this book is h ttp://www.cs.cornell.edu/∼srm/fcg4/. We will con-
tinue to maintain a list of errata and links to courses that use the book, as well as
teaching materials that match the book’s style. Most of the ﬁgures in this book are
in Adobe Illustrator format, and we would be happy to convert speciﬁc ﬁgures into
portable formats on request. Please feel free to contact us at shirley@cs.utah.edu
or srm@cs.cornell.edu.
Acknowledgments
The following people have provided helpful information, comments, or feed-
back about the various editions of this book: Ahmet O ˘guz Aky¨uz, Josh An-
dersen, Zeferino Andrade, Kavita Bala, Adam Berger, Adeel Bhutta, Solomon
Boulos, Stephen Chenney, Michael Coblenz, Greg Coombe, Frederic Cremer,
Brian Curtin, Dave Edwards, Jonathon Evans, Karen Feinauer, Amy Gooch, Eu-
ngyoung Han, Chuck Hansen, Andy Hanson, Razen Al Harbi, Dave Hart, John
Hart, John “Spike” Hughes, Helen Hu, Vicki Interrante, Wenzel Jakob, Doug
James, Henrik Wann Jensen, Shi Jin, Mark Johnson, Ray Jones, Revant Kapoor,
Kristin Kerr, Erum Arif Khan, Mark Kilgard, Dylan Lacewell, Mathias Lang,
Philippe Laval, Marc Levoy, Howard Lo, Joann Luu, Ron Metoyer, Keith Morley,
Eric Mortensen, Koji Nakamaru, Micah Neilson, Blake Nelson, Michael Nikel-
sky, James O’Brien, Steve Parker, Suman ta Pattanaik, Matt Pharr, Peter Poulos,
Shaun Ramsey, Rich Riesenfeld, Nate R obins, Nan Schaller, Chris Schryvers,
Tom Sederberg, Richard Sharp, Sarah Shi rley, Peter-Pike Sloan, Hannah Story,
Tony Tahbaz, Jan-Phillip Tiesel, Bruce Walter, Alex Williams, Amy Williams,
Chris Wyman, and Kate Zebrose.
Ching-Kuang Shene and David Solomon allowed us to borrow their exam-
ples. Henrik Wann Jensen, Eric Levin, Matt Pharr, and Jason Waltman generously
provided images. Brandon Mans ﬁeld helped improve the discussion of hierarchi-
cal bounding volumes for ray tracing. Philip Greenspun (philip.greenspun.com)

Preface xiii
kindly allowed us to use his photographs. John “Spike” Hughes helped improve
the discussion of sampling theory. Wenzel Jakob’s Mitsuba renderer was invalu-
able in creating many ﬁgures. We are extremely thankful to J. W . Baker for help-
ing create the cover Pete envisioned. In addition to being a talented artist, he was
a great pleasure to work with personally.
Many works that were helpful in preparing this book are cited in the chap-
ter notes. However, a few key texts that in ﬂuenced the content and presentation
deserve special recognition here. These include the two classic computer graph-
ics texts from which we both learned the basics: Computer Graphics: Principles
& Practice (Foley, V an Dam, Feiner, & Hughes, 1990) and Computer Graph-
ics (Hearn & Baker, 1986). Other texts include both of Alan Watt’s in ﬂuential
books (Watt, 1993, 1991), Hill’s Computer Graphics Using OpenGL (Francis
S. Hill, 2000), Angel’s Interactive Computer Graphics: A T op-Down Approach
Using OpenGL (Angel, 2002), Hugues Hoppe’s University of Washington dis-
sertation (Hoppe, 1994), and Rogers’ two excellent graphics texts (D. F. Rogers,
1985, 1989).
We would like to especially thank Alic e and Klaus Peters for encouraging
Pete to write the ﬁrst edition of this book and for their great skill in bringing a
book to fruition. Their patience with the aut hors and their dedication to making
their books the best they can be has been instrumental in making this book what
it is. This book certainly would not exist without their extraordinary efforts.
Salt Lake City, Utah
Ithaca, New Y ork
February 2015

---

## 🔗 Related

**来源**: [[fcg]]
**作者**: [[Steve Marschner]] · [[Peter Shirley]]
