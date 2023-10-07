# 🔥 Mathematical Equation of Relative Transform System

## 0. Summary

### 1) Constants

T is period, D is distance, θ is axial tilt, and φ is azimuthal angle (φ(t) = 2πt/T).

### 2) Revolution

$$
\begin{aligned}
Location &= (±D cos \theta cos \psi, ±D sin \psi, ±D sin \theta cos \psi)\\
{dLocation \over dt} &= {2 \pi \over T} \cdot (±D cos \theta sin \psi, ±D cos \psi, ±D sin \theta sin \psi)\\
\end{aligned}
$$

### 3) Rotation

$$
\begin{aligned}
Rotation &= (0, \theta cos \psi, {360 t \over T})\\
{dRotation \over dt} &= (0, -{2 \pi \over T} \cdot \theta sin \psi, {360 \over T})\\
\end{aligned}
$$

## 1. Earth Revolution

### 1) Constants

$$T = {10,800 \over timeScale} s, D = 16,000,000 m, \theta = 23.44 \degree, and \space \psi = {2 \pi t \over T}$$

### 2) Position Allocation

$$Location_{earth} = (D cos \theta cos \psi, -D sin \psi, -D sin \theta cos \psi)$$

### 3) Displacement Integral

$$
\begin{aligned}
Location_{earth} &= Location_{earth} + dt \cdot {dLocation_{earth} \over dt}\\
&= Location_{earth} + dt \cdot {(D cos \theta cos \psi, -D sin \psi, -D sin \theta cos \psi) \over dt}\\
&= Location_{earth} + dt \cdot {2 \pi \over T} \cdot (-D cos \theta sin \psi, -D cos \psi, D sin \theta sin \psi)\\
\end{aligned}
$$

## 2. Earth Rotation

### 1) Constants

$$T = {86,400 \over timeScale} s, \theta = 23.44 \degree, and \space \psi = {2 \pi t \over T}$$

### 2) Position Allocation

$$Rotation_{earth} = (0, \theta cos \psi, {360 t \over T})$$

### 3) Displacement Integral

$$
\begin{aligned}
Rotation_{earth} &= Rotation_{earth} + dt \cdot {dRotation_{earth}\over dt}\\
&= Rotation_{earth} + dt \cdot {(0, \theta cos \psi, {360 t \over T}) \over dt}\\
&= Rotation_{earth} + dt \cdot (0, -{2 \pi \over T} \cdot \theta sin \psi, {360 \over T})\\
\end{aligned}
$$

## 3. Spaceship Rotation

### 1) Constants

$$T = {10,800 \over timeScale} s, \theta = 23.44 \degree, and \space \psi = {2 \pi t \over T}$$

### 2) Position Allocation

$$Rotation_{spaceship} = (0, \theta cos \psi, {360 t \over T})$$

### 3) Displacement Integral

$$
\begin{aligned}
Rotation_{spaceship} &= Rotation_{spaceship} + dt \cdot {dRotation_{spaceship}\over dt}\\
&= Rotation_{spaceship} + dt \cdot {(0, \theta cos \psi, {360 t \over T}) \over dt}\\
&= Rotation_{spaceship} + dt \cdot (0, -{2 \pi \over T} \cdot \theta sin \psi, {360 \over T})\\
\end{aligned}
$$

## 4. Moon Revolution

### 1) Constants

$$T = {2,548,800 \over timeScale} s, D = 384,000,000 m, \theta = 5.14 \degree, and \space \psi = {2 \pi t \over T}$$

### 2) Position Allocation

$$Location_{moon} = Location_{earth} + (D cos \theta cos \psi, -D sin \psi, D sin \theta cos \psi)$$

### 3) Displacement Integral

$$
\begin{aligned}
Location_{moon} &= Location_{earth} + Location_{moon} + dt \cdot {dLocation_{moon} \over dt}\\
&= Location_{earth} + Location_{moon} + dt \cdot {(D cos \theta cos \psi, -D sin \psi, D sin \theta cos \psi) \over dt}\\
&= Location_{earth} + Location_{moon} + dt \cdot {2 \pi \over T} \cdot (-D cos \theta sin \psi, -D cos \psi, -D sin \theta sin \psi)\\
\end{aligned}
$$

## 5. Moon Rotation

### 1) Constants

$$T = {2,548,800 \over timeScale} s, \theta = 5.14 \degree, and \space \psi = {2 \pi t \over T}$$

### 2) Position Allocation

$$Rotation_{moon} = (0, \theta cos \psi, {360 t \over T})$$

### 3) Displacement Integral

$$
\begin{aligned}
Rotation_{moon} &= Rotation_{moon} + dt \cdot {dRotation_{moon}\over dt}\\
&= Rotation_{moon} + dt \cdot {(0, \theta cos \psi, {360 t \over T}) \over dt}\\
&= Rotation_{moon} + dt \cdot (0, -{2 \pi \over T} \cdot \theta sin \psi, {360 \over T})\\
\end{aligned}
$$
