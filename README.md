# Generating Hilbert curves

I was reading about [Hilbert curves](https://en.wikipedia.org/wiki/Hilbert_curve), watched an amazing [video by 3blue1brown](https://www.youtube.com/watch?v=3s7h2MHQtxc) on them and wanted to generate them myself. This is the result. I am using the standard [L-system](https://en.wikipedia.org/wiki/L-system) description of them.

## 2D Hilbert curves
This is [my working Colab](https://github.com/stanislavfort/hilbert-curves/blob/main/Generating_2D_Hilbert_curves.ipynb).
You start with a string `A`. In each iteration, you replace `A` by `+BF-AFA-FB+` and `B` with `-AF+BFB+FA-`. The resulting descriptions look as follows:
- iteration = 0: `A`
- iteration = 1: `+BF-AFA-FB+`
- iteration = 2: `+-AF+BFB+FA-F-+BF-AFA-FB+F+BF-AFA-FB+-F-AF+BFB+FA-+`
- iteration = 3: `+-+BF-AFA-FB+F+-AF+BFB+FA-F-AF+BFB+FA-+F+BF-AFA-FB+-F-+-AF+BFB+FA-F-+BF-AFA-FB+F+BF-AFA-FB+-F-AF+BFB+FA-+F+-AF+BFB+FA-F-+BF-AFA-FB+F+BF-AFA-FB+-F-AF+BFB+FA-+-F-+BF-AFA-FB+F+-AF+BFB+FA-F-AF+BFB+FA-+F+BF-AFA-FB+-+`
  
And so on. These are [turtle graphics](https://en.wikipedia.org/wiki/Turtle_graphics) plotting instructions where `F` means move forward, `+` is a +90 degree turn and `-` is a -90 degree turn. `A` and `B` are ignored when plotting but they are crucial for getting the next iteration of the curve.

Using my [simple Colab](https://github.com/stanislavfort/hilbert-curves/blob/main/Generating_2D_Hilbert_curves.ipynb) you get 2D Hilbert curves like these:

![image](https://github.com/stanislavfort/hilbert-curves/assets/16190914/bb496436-e810-4333-a837-288b9e949b20)

## 3D Hilbert curves
This is [my working Colab](https://github.com/stanislavfort/hilbert-curves/blob/main/Generating_3D_Hilbert_curves.ipynb). The string rewrite rules for 3D Hilbert curves seem less well known and the one I use I found on [Math StackExchange](https://math.stackexchange.com/questions/123642/representing-a-3d-hilbert-curve-as-an-l-system). You start with `X` and replace its each occurence with `^<XF^<XFX-F^>>XFXvF+>>XFX-F>X->` where `<` and `>`represent -+90 degree roll, `-` and `+`represent -+90 degree yaw, and `v` and `^`represent -+90 degree pitch (from [airplane terminology](https://en.wikipedia.org/wiki/Euler_angles#/media/File:Yaw_Axis_Corrected.svg)). I found the angles a bit confusing so here's my implementation in terms of vectors:
```python
def cross_product(a,b):
  return np.array([a[1]*b[2]-a[2]*b[1], a[2]*b[0]-a[0]*b[2], a[0]*b[1]-a[1]*b[0]])

def apply_roll(forward_vector, up_vector, amount):
  assert amount in [-1,1]
  return forward_vector, amount * cross_product(forward_vector,up_vector)

def apply_yaw(forward_vector, up_vector, amount):
  assert amount in [-1,1]
  return amount * cross_product(forward_vector,up_vector), up_vector

def apply_pitch(forward_vector, up_vector, amount):
  assert amount in [-1,1]
  return (-1)**(amount == -1)*up_vector, (-1)**(amount == 1)*forward_vector
```

The resulting curve descriptions are:
- iteration = 0: `X`
- iteration = 1: `^<XF^<XFX-F^>>XFXvF+>>XFX-F>X->`
- iteration = 2: `^<^<XF^<XFX-F^>>XFXvF+>>XFX-F>X->F^<^<XF^<XFX-F^>>XFXvF+>>XFX-F>X->F^<XF^<XFX-F^>>XFXvF+>>XFX-F>X->-F^>>^<XF^<XFX-F^>>XFXvF+>>XFX-F>X->F^<XF^<XFX-F^>>XFXvF+>>XFX-F>X->vF+>>^<XF^<XFX-F^>>XFXvF+>>XFX-F>X->F^<XF^<XFX-F^>>XFXvF+>>XFX-F>X->-F>^<XF^<XFX-F^>>XFXvF+>>XFX-F>X->->`

Using [my simple Colab](https://github.com/stanislavfort/hilbert-curves/blob/main/Generating_3D_Hilbert_curves.ipynb), you get curves like this:

![image](https://github.com/stanislavfort/hilbert-curves/assets/16190914/e13c4a7c-7ee2-4dd4-9740-fd5736dce598)



