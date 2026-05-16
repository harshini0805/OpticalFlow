# Introduction to Optical Flow

## the idea

Most classical computer vision pipelines assume a static scene.

Optical flow tries to estimate how objects move between consecutive image frames.

But instead of directly tracking 3D motion, we estimate the motion of brightness patterns in the image.

> optical flow is what the image *thinks* is moving.

---

## motion field vs optical flow

### motion field

Actual projected motion of 3D scene points onto the image plane.

This depends on:

* object motion
* camera motion
* scene geometry
* perspective projection

### optical flow

Apparent motion of pixel intensities between frames.

What we can actually compute from images.

Important:
optical flow is not always equal to true physical motion.

Things like:

* lighting changes
* reflections
* motion blur
* occlusions

can break the correspondence.

---

## brightness constancy assumption

The core assumption is surprisingly simple:

if a point moves slightly between two nearby frames,
its brightness remains approximately constant.

$$
I(x,y,t)=I(x+u\Delta t,y+v\Delta t,t+\Delta t)
$$

where:

* (u) → horizontal displacement
* (v) → vertical displacement

This assumption is what makes optical flow estimation possible in the first place.

---

## optical flow constraint equation

Using Taylor expansion and local image derivatives, we get:

$$
I_xu + I_yv + I_t = 0
$$

This equation constrains possible motion vectors.

But there’s a problem:

* one equation
* two unknowns

Meaning:
motion cannot be uniquely determined from a single pixel alone.

This is the **aperture problem**.

---

## lucas–kanade intuition

Instead of solving motion independently for each pixel:

* assume neighboring pixels move similarly
* collect information over a local window
* solve jointly

This converts the problem into a least-squares estimation problem.

Main assumptions:

* local spatial coherence
* small motion between frames

The method feels less like “tracking objects” and more like estimating how intensity patterns evolve locally over time.

---

## intuitive connections

While learning this, I kept mentally relating parts of optical flow to concepts I’d seen before.

### Lucas–Kanade → feels a bit like KNN

Not mathematically the same, but the intuition felt similar.

Lucas–Kanade assumes neighboring pixels likely move together and uses a local window to stabilize estimation instead of treating each pixel independently.

That immediately reminded me of KNN-style thinking:

* nearby points tend to share similar properties
* local neighborhoods carry meaningful information
* isolated points are often unreliable

The method basically says:

> don’t trust one pixel alone — ask its neighbors too.

---

### Lucas–Kanade also reminded me of moving averages

A single pixel gives unreliable motion information:
too noisy, too ambiguous.

Lucas–Kanade stabilizes estimation by looking at a neighborhood instead of isolated pixels.

That felt conceptually similar to moving averages:

* local aggregation reduces noise
* nearby information smooths uncertainty
* broader context produces more stable estimates

Instead of averaging values over time,
Lucas–Kanade effectively aggregates motion constraints over space.

The core intuition feels similar:

> don’t trust individual observations too much — trust local structure.

---

### resolution pyramids → strangely reminded me of PCA

The pyramid approach also felt conceptually similar to PCA in spirit.

Instead of working directly with full-resolution complexity:

* we reduce detail
* capture broader/global structure first
* progressively refine finer information later

The coarse-to-fine hierarchy felt similar to how PCA prioritizes dominant large-scale structure before smaller variations.

Again, not mathematically equivalent —
just a useful intuition that helped me understand why pyramids make large motion estimation tractable.

---

## handling large motion

The optical flow equation works best for small displacements.

Large motion breaks the local derivative approximation.

Solution:
image pyramids / resolution pyramids.

Approach:

1. downsample images
2. estimate motion at low resolution
3. propagate estimates upward
4. refine motion progressively at higher resolutions

This is basically a coarse-to-fine optimization strategy.

---

## implementation thoughts

things to implement:

* sparse optical flow with Lucas–Kanade
* dense optical flow with Farneback
* image pyramid visualization
* motion vector field overlays

possible experiments:

* camera motion vs object motion
* low-light robustness
* fast motion failure cases
* occlusion handling
* corners vs edges vs flat regions

---

## personal notes

Optical flow feels deceptively simple at first:
“just track motion between frames.”

But most of the difficulty comes from ambiguity and local assumptions.

Interesting observation:
we never directly measure “motion” itself —
we infer it indirectly through changes in image intensity structure over time.

Another realization:
a lot of ML/CV methods seem to rely on the same recurring idea:

> local structure carries meaningful information.

KNN, moving averages, convolutions, Lucas–Kanade —
different methods, same underlying intuition:
aggregation improves stability.
