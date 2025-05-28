---
layout: post
title:  "PDE Raycaster (preview)"
categories: digest update
---
<pre>
<b>⚠ NOTE: <i>this post is a preview!</i></b>
Many areas throughout are unfinished, unnecessary, or missing entirely.
The page is likely littered with random garbage like a scratch pad.
Code snippets may contain errors, inconsistencies, or complete nonsense as, in many cases, excerpts are taken straight from local projects (possibly years old) so they must be adapted to make sense out of context and revised to promote best practices.
</pre>

# **Hi, folks!**

Lately I've been contending with a bout of emotional strife. My struggle with general anxiety in particular has been exacerbated by the guilt I feel from subjecting the people in my life to the worst of my avoidant tendencies. What's more is that phases of intermittent apathy amidst a seemingly ever-increasing backlog of obligations have left me yearning for a respite. This has prompted me, for better or worse, to seek refuge in the sanctuary of escapism.

<div style="width: 100%; height: 150px; overflow: hidden; border: 1px solid brown;">
    <img alt="Artwork of a person with their hands on either side of their face: swirling arrows emanating from the person's head chaotically permeate the surrounding space. Evocative phrases, 'certainly' and 'I'm stuck', are positioned along the contour of some arrows." src="https://grizzlygrowler.org/wp-content/uploads/2021/10/adhd-image-900x506.jpg" style="margin: -245px 0 0 0;"/>
</div>

<br>

So, without further ado please join me in this babbling stream of consciousness fueled by the onset of hyperfixation! Allow yourself to become entranced by a flame of nostalgia ignited by the spark of an old obsession of mine: ***raycasting!***

<br>

# **What Is Raycasting?**

I'm not certain, but over the years I've got the impression that there exists some contention or, at the very least, some "fuzziness" as to the *exact* boundaries of what may *formally* be considered a raycaster. As a result, I set out to identify some qualities which precisely distinguish raycasting from its derivatives (e.g.: ray marching, ray tracing, etc.) and unambiguously define the relationship between them.

<div>
    <div style="float: right">
        <img alt="A diagram describing the relationships between the concept of raycasting and several of its derivatives flowing from top to bottom as follows: at the very top position is the raycasting class of rendering technique (i.e.: any renderer which represents geometric scene elements visually based on their interactions with view rays). At the next position down, 3 options are shown: Discrete Ray Traversal (a.k.a.: Ray Marching), Continuous Ray Traversal, and Varied Ray Traversal. Arrow lines flow from the top-level raycasting class to the Discrete Ray Traversal option and the Continuous Ray Traversal option suggesting that these options enable 2 distinct forms of raycasting. The 2 distinct options together enable a 3rd option which is a hybrid of discrete and continuous ray traversal. The discrete and varied options both flow into 2 distinct rendering classes at the next position down: Sphere Tracing (i.e.: any raycaster which tests scene elements against a signed distance function at discrete intervals) and Volume Raycasting (i.e.: any raycaster which samples the volume of scene elements at discrete intervals). All 3 options flow into another distinct rendering class at the next position down: Ray Tracing (i.e.: any raycaster which enables light sources and casts subsequent nested rays as a result of the interaction between view rays and scene geometry). At the next position down, 3 options are shown: Light Source Feeler Rays (a.k.a.: Shadow Feelers), Recursive Rays (a.k.a.: indirect lighting), and other material interactions & optical effects. Arrow lines flow from the Ray Tracing class to the 3 options mentioned suggesting that these options enable at least 3 distinct forms of ray tracing. The recursive rays option is further specialized into 2 example variants: perfect mirror (i.e.: where the angle of the new ray is a reflection of the angle of incidence across the surface normal at the location of the initial interaction) and diffuse material (i.e.: where the angle of the new ray is computed as a function of the angle of incidence and the surface normal at the location of the initial interaction; for instance, a random distribution of angles between the angle of incidence and the angle of a perfect reflection may yield results resembling a rough surface or matte material)." src="/assets/raycasting_derivatives.png"/>
    </div>
    For the purposes of this series I'll clarify some of the relevant terms using a framework that makes sense to me. Thus, we'll proceed with the caveat that this framework is based on my own mental model which may not be consistent with other sources or the broader consensus among industry folks and/or other hobbyists. I invite any feedback to help improve my understanding and, ultimately, this series for the benefit of anyone following along!
</div>

<br>

#### **My Interpretation**

In general, I consider raycasting to be a broad class of computer graphics rendering techniques all characterized by their ability to visualize geometric entities in a scene based on those entities' interactions with "view rays". From this perspective, anything which tests **objects** for intersection with **view rays** and then renders some representation of those intersections is a raycaster.

What distinguishes various raycasting derivatives from one another, in my estimation, lies within the finer details: _how do we arrive at an intersection in the first place and how do we represent the resulting interaction and any subsequent interactions?_

<img alt="Animated GIF showing a scene from a pseudo-3D, 1st person perspective alongside the same scene shown from a 2D, overhead-like perspective as the field of view can be seen rotating about the viewer's vertical axis from both perspectives." src="https://upload.wikimedia.org/wikipedia/commons/e/e7/Simple_raycasting_with_fisheye_correction.gif" width="100%"/>

# **My First Raycaster**
<img alt="Animated GIF showing a texture-mapped scene from a pseudo-3D, 1st person perspective as the view pans along the scene vertically." src="/assets/ll3d_raycaster.gif" width="100%"/>

I have written a raycaster in the past. Granted, it was a certifiable mess, but it served its purpose as a worthwhile learning experience. It featured texture-mapped walls, vertical camera panning via [Y-shearing](https://zdoom.org/wiki/Y-shearing) in addition to neat post-processing effects like a reflective "water" floor effect as well as a frame-blending-style motion blur effect.
```java
public class Edges {
    // ...

    public static float[] intersection(
        float xa1, float ya1,
        float xb1, float yb1,
        float xa2, float ya2,
        float xb2, float yb2
    ) {
        float r1=(xb1-xa1);
        float r2=(xb2-xa2);
        float m1=(yb1-ya1)/r1;
        float m2=(yb2-ya2)/r2;
        float y1Int=ya1-xa1*m1;
        float y2Int=ya2-xa2*m2;
        // NOTE: replace "==" & "!=" with threshold
        if(m1!=m2)//slopes are not equal; not parallel
        {
            float x;
            float y;
            if(r1==0)//vertical line 1
            {
                x=xa1;
                y=m2*x+y2Int;
            }
            else if(r2==0)//vertical line 2
            {
                x=xa2;
                y=m1*x+y1Int;
            }
            else
            {
                x=(y2Int-y1Int)/(m1-m2);
                y = m1*x+y1Int;
            }
            //if(Math.signum(x-xa1)==Math.signum(ra) && Math.signum(y-ya1)==Math.signum(yb1-ya1))
            float[] hit={x,y,(xa2-x)*(xa2-x)+(ya2-y)*(ya2-y)};
            if(isInView(hit) && isOnEdge(hit,xa2,ya2,xb2,yb2)) {
                return hit;
            }
        }
        return null;
    }

    private static boolean isOnEdge(float[] hit, float xa, float ya, float xb, float yb)
    {
        //*
        float dotProduct = (hit[0] - xa) * (xb - xa) + (hit[1] - ya)*(yb - ya);
        if (dotProduct < 0) return false;
        float lengthSqrdba = (xb - xa)*(xb - xa) + (yb - ya)*(yb - ya);
        if (dotProduct > lengthSqrdba) return false;
        return true;
    }

    private static boolean isInView(float[] hit)
    {
        return hit[1] > 0;
    }

    // ...
}
```

By testing the equations of each ray with that of each element of the unorganized list of line segments and then either sorting ([painter's algorithm](https://en.wikipedia.org/wiki/Painter%27s_algorithm)) or filtering ([Z-buffering](https://en.wikipedia.org/wiki/Z-buffering)) the resulting set of intersection points by distance to the camera we are able to solve [the visibility problem](https://en.wikipedia.org/w/index.php?title=Visibility_problem&redirect=yes) by brute force ultimately enabling us to render the nearest edge in view if one exists. Because we are essentially solving for the intersection point of a pair of arbitrary, infinite lines, it means that we impose no restrictions on the orientation (position, angle, length or otherwise) of scene elements ("edges" or "walls" if you prefer) relative to one another. This is in contrast to what most of us may be accustomed to observing among titles from the primordial era of 3D games which utilized raycasting. Specifically, consider the likes of Hovertank 3D, Catacomb 3D, Wolfenstein 3D and its derivatives, etc.

![/assets/pde_raycaster_3x_480p.gif](/assets/pde_raycaster_3x_480p.gif)

<pre>
<b>⚠ NOTE: <i>if you stopped by to check out this preview I appreciate it!</i></b>
Soon I'll add the full digest post serving as the overview of a series of posts I intend to create which will include additional narrative entries with code snippets to follow along with in your local project!
</pre>
