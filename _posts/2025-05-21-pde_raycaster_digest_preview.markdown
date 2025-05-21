---
layout: post
title:  "PDE Raycaster (preview)"
categories: digest update
---
# Hi, folks!

Lately I've been contending with a bout of emotional strife and general anxiety exacerbated by chronic, intermittent apathy. This has caused me to seek refuge in the sanctuary of escapism. With the flame of nostalgia and hyper-fixation by my side I began revisiting an old obsession of mine: **raycasting**!

![/assets/ll3d_raycaster.gif](/assets/ll3d_raycaster.gif)

I have written a raycaster in the past. Granted, it was a certifiable mess, but it served its purpose as a worthy learning experience. It featured texture-mapped walls, vertical camera panning via Y-shearing in addition to neat post-processing effects like a reflective "water" floor effect as well as a frame-blending-style motion blur effect. It worked by comparing each individual view ray (1 per vertical column of pixels on the screen) against all elements of a 2D scene comprised of unstructured line segments or "Edges". The objective of such a comparison is to derive the point, if one exists, at which the element in question intersects our line of site.

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
            if(!EyeContains(hit,xa1,ya1,xb1,yb1) || !EdgeContains(hit,xa2,ya2,xb2,yb2))
                return null;//hit;
            else
                return hit;//null;
        }
        return null;
    }
    
    private static boolean EdgeContains(float[] hit, float xa, float ya, float xb, float yb)
    {
        //*
        float dotProduct = (hit[0] - xa) * (xb - xa) + (hit[1] - ya)*(yb - ya);
        if (dotProduct < 0) return false;
        float lengthSqrdba = (xb - xa)*(xb - xa) + (yb - ya)*(yb - ya);
        if (dotProduct > lengthSqrdba) return false;
        return true;
    }
    
    private static boolean EyeContains(float[] hit)
    {
        return hit[1] > 0;
    }
    
    // ...
}
```

By testing the equations of each ray with that of each element of the unorganized list of line segments and then either sorting ([painter's algorithm](https://en.wikipedia.org/wiki/Painter%27s_algorithm)) or filtering ([Z-buffering](https://en.wikipedia.org/wiki/Z-buffering)) the resulting set of intersection points by distance to the camera we are able to solve [the visibility problem](https://en.wikipedia.org/w/index.php?title=Visibility_problem&redirect=yes) by brute force ultimately enabling us to render the nearest edge in view if one exists. Because we are essentially solving for the intersection point of a pair of arbitrary, infinite lines, it means that we impose no restrictions on the orientation (position, angle, length or otherwise) of scene elements ("edges" or "walls" if you prefer) relative to one another. This is in contrast to what most of us may be accustomed to observing among titles from the primordial era of 3D games which utilized raycasting. Specifically, consider the likes of Hovertank 3D, Catacomb 3D, Wolfenstein 3D and its derivatives, etc.

![/assets/pde_raycaster_3x_480p.gif](/assets/pde_raycaster_3x_480p.gif)

# **...WIP!**
*If you stopped by to check out this preview I appreciate it! Soon I'll add the full digest post serving as the overview of a series of posts I intend to create which will include additional narrative entries with code snippets to follow along with in your local project!*