# The Overview Effect

## DEMO

<div align="center">
    <a href="https://www.youtube.com/watch?v=W4fpbuXzEPc">
        <img src="/ReadmeAssets/Thumbnail/Thumbnail 4.png">
    </a>
</div>

## Phase 1: Concept and Design

<!-- Brainstorm a concept for your VR experience, drawing inspiration from the articles.
Sketch a basic design for your virtual environment, considering elements like nature, Earth, space, wildlife, humanity, and the emotional impact you want to convey. -->

Inspired of following two reference, I decided to design the earth system simulator in which rotation and revolution of satelite, moon, and earth is implemented precisely. The one can have the overview effect from overviewing the movement of overwhelming orbs throughout whole gameplay experience.

<blockquote>
The overview effect is a cognitive shift reported by some astronauts while viewing the Earth from space. Researchers have characterized the effect as "a state of awe with self-transcendent qualities, precipitated by a particularly striking visual stimulus". The most prominent common aspects of personally experiencing the Earth from space are appreciation and perception of beauty, unexpected and even overwhelming emotion, and an increased sense of connection to other people and the Earth as a whole. The effect can cause changes in the observer’s self concept and value system, and can be transformative.
</blockquote>

<div align="right">
    <a href="https://en.wikipedia.org/wiki/Overview_effect">https://en.wikipedia.org/wiki/Overview_effect</a>
</div>

<br>

<blockquote>
The Edgar Mitchell VR experience invites the world to join the Apollo 14 Astronaut on his mission to the Moon and into his most intimate moment of connection to life itself.
As we journey through space, we will hear Edgar’s voice guide us through the dissolving of any and all boundaries within our consciousness to the universal connection we humans share.
In concert with stunning images of the cosmos, historic NASA audio, carefully crafted sound design, sensation-inducing haptic gear, and epic music, the Edgar Mitchell VR Experience will take users on a meditative, visceral experience of transcendence.
Sit comfortably, quiet your mind, and relax.
</blockquote>

<div align="right">
    <a href="https://store.steampowered.com/app/1445700/The_Edgar_Mitchell_Overview_Effect_VR_Experience/">https://store.steampowered.com/app/1445700/The_Edgar_Mitchell_Overview_Effect_VR_Experience/</a>
</div>

<br>

<p align="center">
    <img src="/ReadmeAssets/Notebook1.jpg" width="25%">
    <img src="/ReadmeAssets/Notebook2.jpg" width="25%">
    <img src="/ReadmeAssets/Notebook3.jpg" width="25%">
</p>

## Phase 2: Asset Compilation

<!-- Use readily available 3D models, textures, and assets (or create simple ones) to build your virtual environment.
Ensure that the assets align with your chosen theme and design. -->

### 1. Moon

ColorTIF.tif, MoonMaterial.uasset

### 2. Earth

BlueMarble., Cloud.tif, BlackMarble.tif, Specular.png, EarthMaterial.uasset

### 3. Universe

UniverseJPEGHDR.hdr, UniverseMaterial.uasset

### 4.. Sound

First Step.wav

### 5. Others

SolidSphere.uasset, GlassMaterial.uasset, GrabComponent.uasset, TimeScaleUI.uasset

## Phase 3: VR Development

<!-- Using the Unreal Engine VR Template, begin constructing your first environment.
Implement basic user interactions, such as the ability to look around and navigate the space. -->

### 1. Absolute Transform System

#### 1) Period Ratio

I will implement this project in the exact same time ratio of the earth system. I investigated the period of satelite rotation, satelite revolution, moon rotation, moon revolution, and earth rotation. The simulation will start with the time scale 1.0, which means the system is simulating as same as real time. Of course time scale will be able to controlled by player. Player will be able to increase or decrease a time scale with controller action.

According to the orbital mechanics, the revolution period and orbit radius of specific satelite is determined by the single physical value, mass. I set the suitable value of satelite mass in this simulation. Revolution period of the satelite is 3h, and orbit radius of the satelite is 16,000km.

https://en.wikipedia.org/wiki/Orbital_mechanics

$$
\begin{aligned}
Period_{satelite}^{rot} &\approx 3 h = 10,800 s\\
Period_{satelite}^{rev} &\approx 3 h = 10,800 s\\
Period_{moon}^{rot} &\approx 29.5 d = 2,548,800 s\\
Period_{moon}^{rev} &\approx 29.5 d = 2,548,800 s\\
Period_{earth}^{rot} &\approx 24 h = 86,400 s\\
\end{aligned}
$$

$$
Period_{satelite}^{rot} : Period_{satelite}^{rev} : Period_{moon}^{rot} : Period_{moon}^{rev} : Period_{earth}^{rot}\\
\approx 1 : 1 : 236 : 236 : 8 \space\\
$$

#### 2) Radius Ratio

I will implement this project in the exact same space ratio of the earth system. I investigated the radius of moon, earth, satelite orbit, and moon orbit. Since the unit increases exponentially when it comes to the astronomical view, the unreal engine had to handle numerical computation. It invoked too much latency at first, but after some changes to setting and a few callibration, I can simulate it without meaningful latency.

I used approximated value of radius to reduce latency and simplify the computation. Radius of moon is approximated from 1,738km to 1,600km. Radius of earth is approximated from 6,378km to 6,400km. Radius of moon orbit is approximated from 384,405km to 384,000km.

$$
\begin{aligned}
Radius_{moon}^{sphere} &\approx 1,600,000 m\\
Radius_{earth}^{sphere} &\approx 6,400,000 m\\
Radius_{satelite}^{orbit} &\approx 16,000,000 m\\
Radius_{moon}^{orbit} &\approx 384,000,000 m\\
\end{aligned}
$$

$$
Radius_{moon}^{sphere} : Radius_{earth}^{sphere} : Radius_{satelite}^{orbit} : Radius_{moon}^{orbit}\\
\approx 1 : 4 : 10 : 240
$$

<p align="center">
    <img src="/ReadmeAssets/RadiusRatio.png">
</p>

#### 3) Absolute Transform System

Absoulte transform system reflects general system of satelite, moon, and earth translation. This translation contains rotation and revolution. In Absolute Transform System, pivot of the system is the earth. Earth is located at coordinate (0, 0, 0) and its location never changes. Only its rotation can be changed due to the rotation. Satelite and moon will conduct a revolution around the earth. They will conduct rotation simultaneously, too.

Since every orbs rotate and revole while their axis tilted, we have to apply their axial tilt to this simulation to implement the system with exactly same movement of the earth system. With some background of trigonometry, calculus, and classical mechanics, the one can calculate the location and rotation of each orb at the given time t from the start of simulation.

https://en.wikipedia.org/wiki/Orbital_mechanics

Also we can calculate the location and rotation with two different method. One is position allocation, and the other is displacement integral. Unreal engines updates the condition of every object in the level every tick before rendering the scene to the user.

Position allocation literally allocates the position of the given orbit according to the equation every single tick. It calculates the location vector and rotation vector of specific orb each tick and allocates the value to the corresponding object. In contrast, displacement integral calculates the derivation value of the location function, and simply adds the value to the data structure which stores the value of location vector and rotation vector of each orbs every single tick.

Every method of cacluating transform of satelite, moon, and earth under absolute transform system is described in the following link. You can simply rewrite each of them into blueprint visual scripting or C++ code in order to implement the absolute transform system in your unreal engine.

<a href="Absolute.md" target="_blank">🔥 Mathematical Equation of Absolute Transform System</a>

<details>
<summary>Blueprint Implementation</summary>

##### Spaceship Revolution

![](/ReadmeAssets/TransformSystem/SpaceshipRevolution.png)

##### Spaceship Rotation

![](/ReadmeAssets/TransformSystem/SpaceshipRotation.png)

##### Earth Rotation

![](/ReadmeAssets/TransformSystem/EarthRotation.png)

##### Moon Revolution

![](/ReadmeAssets/TransformSystem/MoonRevolution.png)

##### Moon Rotation

![](/ReadmeAssets/TransformSystem/MoonRotation.png)

</details>

### 2. Material of Moon, Earth, and Universe

#### 1) Moon

Material of moon is simply created with a single surface image of the mmon. Since the sunlight is coming from $x = -\inf$ to $x = \inf$, the brightness of given moon area is determined based on the sign of x-axis direction local offset. The image of moon can be downloaded from the following website.

https://svs.gsfc.nasa.gov/cgi-bin/details.cgi?aid=4720

#### 2) Earth

Material of earth is created with four different masked images of the earth. Images consist of surface image of the earth during day, surface image of the earth during night, masked binary image of the earth in which cloud is processed as 1 and other is processed as 0, and masked binary image of the earth in which continent is processed as 1 and other is processed as 0.

First, we can divide the earth into two area, based on the sign of x-axis direction local offset. Since the sunlight is coming from $x = -\inf$ to $x = \inf$, we can allocate the day surface image into the area with minus local offset, and night surface image into the area with plus local offset.

Second, we have to apply cloud to the material. Since the color of cloud is white in day and black in night, we can use the original version and negated version of masked binary image of the cloud in order to allocate the cloud color appropriately based on the sign of x-axis direction local offset.

Third, we have to give specular and roughness value to the material. specular value refers to how much metalic the material is, and roughness value refers to how much roughness the material is. The more specular value the material has, the more amount of light it will reflex. The more roughness value the material has, the more dense the light will be.

Since the night area of the earth doesn't reflex the sunlight at all, we can give the specular value 0 to the night area of the earth. Also because the continent and cloud area reflex much less amount of sunlight than the ocean area, we can give very low specular value to the continent and cloud area and suitable specular value to the ocean area, by using masked binary image of cloud and continent.

The continent and the cloud is much more rough than the ocean, which means that the reflexed light from the continent and the cloud should be much less denser than the ocean. We can set the roughness value accordingly by using masked binary image of cloud and continent.

Fourth, we should implement the atmosphere of the planet earth. The reason that the sky is blue is because the sunlight scatters when it meets the atmosphere due to the difference of refractive index of the space, which is vaccum, and the atmosphere. As we can see the scattering of the sunlight in the earth, astronauts can also see the scattering of the sunlight in the space, too. Scattering can't happen when there is no sunlight. Therefore, we can set suitable blue emmersive color to day area of the earth using the sign of x-axis direction local offset.

https://visibleearth.nasa.gov/images/74518/december-blue-marble-next-generation-w-topography  
https://visibleearth.nasa.gov/images/57747/blue-marble-clouds  
https://www.visibleearth.nasa.gov/images/144898/earth-at-night-black-marble-2016-color-maps  
https://www.shadedrelief.com/natural3/pages/extra.html

#### 3) Universe

The universe material is made of one image, covering the entire southern and northern celestial sphere, reveals the cosmic landscape that surrounds our tiny blue planet. It is applied to the skybox in the simulation and callibrated by three variables, power, scene brighness, and star brightness. The image is divided into scene and star based on each pixel's brightness. We can manipulate scene brightness and star brightness to suitable value in order to make the background space realistic. Then, the power value controls the overall brightness of the image. The original image can be downloaded from the following link.

https://www.eso.org/public/images/eso0932a/

<details>
<summary>Blueprint Implementation</summary>

##### Earth Material 1

![](/ReadmeAssets/Material/EarthMaterial1.png)

##### Earth Material 2

![](/ReadmeAssets/Material/EarthMaterial2.png)

##### Earth Material 3

![](/ReadmeAssets/Material/EarthMaterial3.png)

##### Moon Material

![](/ReadmeAssets/Material/MoonMaterial.png)

##### Universe Material

![](/ReadmeAssets/Material/UniverseMaterial.png)

</details>

### 3. Time Scale

#### 1) Theory

If you read the "🔥 Mathematical Equation of Absolute Transform System" page above, you would have noticed every single equation has a parameter named time scale. Usually it is used to divide the period of every revolution and rotation that exists, in order to accelerate them as much as its value. Since we have implemented each movement including the global variable time scale, It seems like all we have to do is only manipulating its value if user input comes.

However, there is one fatal problem with this. The phase of the trigonometric function changes if we changes only time scale value. Okay, I know it is hard to understand at first. So let me explain them with an example. Assume we are simulating a periodical movement with a trigonometric function $sin({\pi t \over T}), where \space T = {3 \over timeScale}$. Initial time scale is $1$ and user wants to increase the time scale as $1.5$ when $t = 3$. In easy word, we want to change a function from $f(x)$ to $g(x)$ in figure below.

<p align="center">
    <img src="/ReadmeAssets/TimeScale.png">
</p>

If we just change the time scale value, in other word just move from $f(x)$ to $g(x)$, the current phase of funciton process varies. For $f(x)$, $t = 3$ refers to the function has proceeded as $0.5T$, its current value is 0 and decreasing. However, for $g(x)$, $t = 3$ refers to the function has proceeded as $0.75T$, its current value is -1 and increasing. This is the difference of the phase that occurs if we change only the time scale value, and it is fatal because the only thing we want to change is time scale value and we must preserve the phase of the function in order to preserve the phase of revolution and rotaion. Otherwise, the satelite and moon will change the direction of revolving too dramatic so that it could literally crashes the earth. (Not joking, it really happened in simulation)

So how can we preserve the phase of the function? we can easily divide the timer value itself in order to move horizontally in the function. In the example above, we can multiply the value $timeScale_{curr} \over timeScale_{next}$, which is same as ${1 \over 1.5} = {2 \over 3}$ to the timer value. Then, the timer value changes from $3$ to $2$, and the phase of the function remain not changed in spite of time scale change.

#### 2) Control Time Scale

User can control the time scale in two different ways. First is increasing the time scale by doubling it. Second is decreasing the time scale by halfing it. Initial time scale value is 1 and user can manipulate it from 1 to 1024. When increasing time scale, time scale doubles and timer halfs. When decreasing time scale, time scale halfs and timer doubles. It preserves the current phase of orb movement despite of the change of time scale.

$$
t = t \cdot {timeScale_{curr} \over timeScale_{next}}\\
timeScale_{curr} = timeScale_{next}\\
$$

<details>
<summary>Blueprint Implementation</summary>

##### Increase Time Scale

![](/ReadmeAssets/ControlTimeScale/IncreaseTimeScale.png)

##### Decrease Time Scale

![](/ReadmeAssets/ControlTimeScale/DecreaseTimeScale.png)

</details>

## Phase 4: Emotional Impact

<!-- Students will focus on creating an emotional impact in their VR experience, drawing from the readings.
Consider using lighting, sound, and storytelling elements to evoke specific emotions within users. -->

[![](/ReadmeAssets/Thumbnail/Thumbnail%201.png)](https://youtu.be/kAeelf3THbk)

## Phase 5: Testing and Iteration

<!-- Test your VR experience to ensure it runs smoothly and effectively.
Gather feedback from peers and make necessary adjustments to enhance the user experience. -->

### 1. Relative Transform System

Absolute transform system represents revolution and rotation of orbs perfectly. However, it has a huge flaw. Since the location of satelite varies every single tick, the location of player have to be varied exactly as much as the location variation of the satelite. And this value should be callibrated with the offset made by the movement of player, too. We have to save the value of player movement into specific data structure, and keep tracking of it in order to implement player movement within the absolute transform system. Not only it is hard to implement, but also it consumes a lot of computing resources and provoke high amount of latency.

### 2. User Interface, and User Experience

Although I have implemented algorithm of manipulating time scale, there is not appropriate user interface or user experience to control time scale. I have to design some user interface and make user experience flow in order to intuitively convey the method of controlling the time scale. Also, I will implement some additional functionalities for user experience such as navigation and generating grabbable object.

## Phase 3: VR Development

<!-- Using the Unreal Engine VR Template, begin constructing your first environment.
Implement basic user interactions, such as the ability to look around and navigate the space. -->

### 1. Relative Transform System

Relative transform system successfully solves the problem with absolute transform system. Rather than constantly locating the earth at the center, it constantly locates the satelite at the center. This solves the problem effectively since the player have not to move millions of meter in the level now. But how it is possible to constantly locate the player at the center? Don't we have to move the satelite every tick according to the revolution of satelite?

It can be accomplished thanks to the magic of coordinate system. Can you distinguish the difference between two stopped car and two car that is running with the same speed, 100km/h if you can only see the opponent car and no background? Let me give another example. Assume you are in the train and the only thing you can is the background nature is passing behind rapidly in constant speed. Can you determine whether your train is moving forward or the background nature is moving backward? In inertial frame of reference, the one cannot determine the subject of movement since every movement is relative.

https://en.wikipedia.org/wiki/Inertial_frame_of_reference

So if we rotate and revolve the earth around the satelite with the exact opposite velocity of the one of absolute transform system, the player cannot distinguish whether the satelite is revolving around the earth or the earth is revolving around the satelite.

However, it comes somewhat difficult if it comes to the movement of the moon. The moon revolves around the earth, not the satelite, so it needs one more step to work properly. We can add the affect of rotation and revolution of moon under relative transform system to the current location of the earth calculated under the same system. So that we can make the illusion that the moon is revolving around the earth, not the satelite.

Also, we can apply the same two different method for caculating location and rotation of orbits under relative transform system, position allocation and displacement integral. The description is included in the following link. Same as absolute transform system, you may understand and implement the content yourself with some background knowledge of trigonometry, calculus, and classical phsycis.

<a href="Relative.md" target="_blank">🔥 Mathematical Equation of Relative Transform System</a>

<details>
<summary>Blueprint Implementation</summary>

##### Earth Revolution

![](/ReadmeAssets/TransformSystem/EarthRevolution.png)

##### Earth Rotation

![](/ReadmeAssets/TransformSystem/EarthRotation.png)

##### Spaceship Rotation

![](/ReadmeAssets/TransformSystem/SpaceshipRotation.png)

##### Moon Revolution

![](/ReadmeAssets/TransformSystem/MoonRevolution.png)

##### Moon Rotation

![](/ReadmeAssets/TransformSystem/MoonRotation.png)

</details>

### 2. User Interface, and User Experience

<p align="center">
    <img src="/ReadmeAssets/Controller.png">
</p>

#### 1) Time Scale

I have designed user interface and user experience flow for controlling time scale. If user start pressing button X at left controller, user interface hologram that shows current time scale and some futuristic spaceship effect opens in front of the spaceship. If user complete pressing button X at left controller, the user interface closes. While user pressing button X at left controller, user can start pressing button B or A at right controller in order to increase or decrease the time scale.

#### 2) Grabbable

User can generate grabbable object in front of current control rotation of the user by pressing button Y at left controller. User can actaully grab the object and physically interact with the object freely with both controller while playing simulation in VR device. And since you are in the spaceship, where the effect of gravity vanishes since the virtual centrifugal force cancel out the gravity, every single object doesn't affected by gravity.

#### 3) Navigation

User can navigate around the preset translucent circular area, which is temporary implemented as spaceship, by controlling joystick at right controller.

<details>
<summary>Blueprint Implementation</summary>

##### Time Scale UI

![](/ReadmeAssets/UIUX/TimeScaleUI.png)

##### Get Text Block Text

![](/ReadmeAssets/UIUX/GetTextBlockText.png)

##### Get Circular Bar Value

![](/ReadmeAssets/UIUX/GetCircularBarValue.png)

##### Open Time Scale UI

![](/ReadmeAssets/UIUX/OpenTimeScaleUI.png)

##### Close Time Scale UI

![](/ReadmeAssets/UIUX/CloseTimeScaleUI.png)

##### Generate Grab Object

![](/ReadmeAssets/UIUX/GenerateGrabObject.png)

</details>

## Phase 4: Emotional Impact

<!-- Students will focus on creating an emotional impact in their VR experience, drawing from the readings.
Consider using lighting, sound, and storytelling elements to evoke specific emotions within users. -->

[![](/ReadmeAssets/Thumbnail/Thumbnail%202.png)](https://youtu.be/aXk-zJEC7uA)

[![](/ReadmeAssets/Thumbnail/Thumbnail%203.png)](https://youtu.be/gL8F-LTYUYM)

## Phase 6: Documentation and Presentation

<!-- Create documentation, including a brief project description, screenshots of your experience, a list of assets used, and a reflection on the emotional impact you aimed to achieve.
Present your VR experiences to the class, sharing your inspirations and insights. -->

### 1. How to Play

#### 1) Hardware Requirement

- Oculus Quest 2
- CPU: Intel i5-4590 / AMD Ryzen 5 1500x
- GPU: NVIDIA GTX 1050 Ti / AMD Radeon RX 470
- RAM: 8GB
- OS: Windows 10

#### 2) Software Requirement

- Oculus App
- Steam
- Steam VR
- Unreal Engine 5.3

#### 3) Instruction

1. git clone https://github.com/hoosong0235/The-Overview-Effect.git
2. Download https://drive.google.com/file/d/1emNAML0hI4qhOL5moOKxqKwioE8SN-zN/view?usp=sharing
3. Move Assets/ to Content/Main/
4. Enjoy :)

#### 4) Tutorial

https://www.meta.com/ko-kr/help/quest/articles/headsets-and-accessories/oculus-link/connect-link-with-quest-2/  
https://www.youtube.com/watch?v=Z4sClxhgsxk&t=359s

### 2. Reference

#### 1) Video

https://www.udemy.com/course/unrealcourse-korean  
https://www.youtube.com/watch?v=dqwpQarrDwk  
https://www.youtube.com/watch?app=desktop&v=5qXEI79LmTw  
https://www.youtube.com/watch?v=Qoa6hdxGOL8  
https://www.youtube.com/watch?v=otOPdOHWqWY&t=164s  
https://www.youtube.com/watch?v=nxEluE1s3Xk  
https://www.youtube.com/watch?v=wqjJU4V6bGM&t=621s  
https://www.youtube.com/watch?v=u3yk75fUR-U  
https://www.youtube.com/watch?v=TXQhGjVZJ-8

#### 2) Article

https://virtualrealitypop.com/the-overview-effect-and-virtual-reality-a7beed4ff2c4  
https://variety.com/2022/tv/news/william-shatner-space-boldly-go-excerpt-1235395113/  
https://en.wikipedia.org/wiki/Overview_effect  
https://www.spinlaunch.com/
https://store.steampowered.com/app/1445700/The_Edgar_Mitchell_Overview_Effect_VR_Experience/
https://forums.unrealengine.com/t/simple-earth-material-tutorial-4-7-6/30738  
https://en.wikipedia.org/wiki/Orbital_mechanics  
https://en.wikipedia.org/wiki/Inertial_frame_of_reference

#### 3) Asset

https://www.youtube.com/watch?v=HV1CMDhemt0  
https://svs.gsfc.nasa.gov/cgi-bin/details.cgi?aid=4720  
https://visibleearth.nasa.gov/images/74518/december-blue-marble-next-generation-w-topography  
https://visibleearth.nasa.gov/images/57747/blue-marble-clouds  
https://www.visibleearth.nasa.gov/images/144898/earth-at-night-black-marble-2016-color-maps  
https://www.shadedrelief.com/natural3/pages/extra.html  
https://www.eso.org/public/images/eso0932a/
