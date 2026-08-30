# 🖐️ Gesture Recognition — Infinite Gauntlet

A real-time hand landmark visualization project built with **React, JavaScript, TensorFlow.js, Handpose, Fingerpose, and React Webcam**.

The project uses a webcam to detect a user's hand and visualizes the detected **21 hand landmarks** on a canvas with an **Infinite Gauntlet-inspired color scheme**.

---

## 🚀 Features

* 📷 Real-time webcam input
* 🧠 Hand detection using TensorFlow.js
* ✋ Detection of 21 hand landmarks
* 🎨 Infinite Gauntlet-style landmark colors
* 🖌️ Canvas-based hand skeleton visualization
* ⚛️ Built with React
* 🔥 Gesture recognition support using Fingerpose

---

## 🛠️ Tech Stack

* **React**
* **JavaScript**
* **Vite**
* **TensorFlow.js**
* **TensorFlow Handpose**
* **Fingerpose**
* **React Webcam**
* **HTML Canvas**

---

## 📦 Installation

Clone the repository:

```bash
git clone <your-repository-url>
```

Navigate into the project:

```bash
cd GestureRecognition
```

Install dependencies:

```bash
npm install
```

Start the development server:

```bash
npm run dev
```

The application will be available at the local URL provided by Vite.

---

# 🧠 How It Works

The application follows this basic pipeline:

```text
                 Webcam
                   │
                   ▼
            React Webcam
                   │
                   ▼
             TensorFlow.js
                   │
                   ▼
              Handpose
                   │
                   ▼
        21 Hand Landmarks
                   │
             ┌─────┴─────┐
             ▼           ▼
          Canvas      Fingerpose
             │           │
             ▼           ▼
       Draw Hand      Recognize
       Skeleton        Gesture
```

The webcam provides the video stream.

TensorFlow.js and Handpose analyze the video and return the coordinates of the detected hand landmarks.

The canvas is then used to draw the hand skeleton and landmark points.

Fingerpose can be used to interpret these landmarks as recognizable gestures such as:

* 👍 Thumbs Up
* ✌️ Victory
* ✊ Fist
* 👋 Wave
* and other custom gestures

---

# ✋ Hand Landmarks

The Handpose model provides **21 landmarks** for a detected hand.

They are organized as follows:

```text
                 8
                 │
                 7
                 │
                 6
                 │
                 5
                 │
                 0 ────────────────┐
                /                 │
               /                  │
              9                  13
              │                   │
             10                  14
              │                   │
             11                  15
              │                   │
             12                  16

        17 ─ 18 ─ 19 ─ 20
```

### Landmark Indexes

|   Index | Landmark      |
| ------: | ------------- |
|     `0` | Wrist         |
|   `1–4` | Thumb         |
|   `5–8` | Index Finger  |
|  `9–12` | Middle Finger |
| `13–16` | Ring Finger   |
| `17–20` | Pinky         |

---

# ☝️ Finger Joint Mapping

The `fingerJoints` object defines which landmarks belong to each finger and the order in which they should be connected.

```javascript
const fingerJoints = {
  thumb: [0, 1, 2, 3, 4],
  indexFinger: [0, 5, 6, 7, 8],
  middleFinger: [0, 9, 10, 11, 12],
  ringFinger: [0, 13, 14, 15, 16],
  pinky: [0, 17, 18, 19, 20],
};
```

This allows the application to connect the landmarks and create the hand skeleton.

For example:

```text
0 → 5 → 6 → 7 → 8
```

represents the connection from the wrist to the tip of the index finger.

---

# 💎 Infinite Gauntlet Style

Each landmark is assigned a different color and size.

```javascript
const style = {
  0: { color: "yellow", size: 15 },

  1: { color: "gold", size: 6 },
  2: { color: "green", size: 10 },
  3: { color: "gold", size: 6 },
  4: { color: "gold", size: 6 },

  5: { color: "purple", size: 10 },
  6: { color: "gold", size: 6 },
  7: { color: "gold", size: 6 },
  8: { color: "gold", size: 6 },

  9: { color: "blue", size: 10 },
  10: { color: "gold", size: 6 },
  11: { color: "gold", size: 6 },
  12: { color: "gold", size: 6 },

  13: { color: "red", size: 10 },
  14: { color: "gold", size: 6 },
  15: { color: "gold", size: 6 },
  16: { color: "gold", size: 6 },

  17: { color: "orange", size: 10 },
  18: { color: "gold", size: 6 },
  19: { color: "gold", size: 6 },
  20: { color: "gold", size: 6 },
};
```

The larger colored points represent the major joints of each finger.

```text
Wrist       → Yellow
Thumb       → Green
Index       → Purple
Middle      → Blue
Ring        → Red
Pinky       → Orange
Finger joints → Gold
```

---

# 🎨 Drawing the Hand

The `drawHand` function is responsible for visualizing the detected hand on the canvas.

```javascript
export const drawHand = (predictions, ctx) => {
  if (predictions.length > 0) {
    predictions.forEach((prediction) => {
      const landmarks = prediction.landmarks;

      // Draw finger connections
      for (let j = 0; j < Object.keys(fingerJoints).length; j++) {
        let finger = Object.keys(fingerJoints)[j];

        for (let k = 0; k < fingerJoints[finger].length - 1; k++) {
          const firstJointIndex = fingerJoints[finger][k];
          const secondJointIndex = fingerJoints[finger][k + 1];

          ctx.beginPath();

          ctx.moveTo(
            landmarks[firstJointIndex][0],
            landmarks[firstJointIndex][1]
          );

          ctx.lineTo(
            landmarks[secondJointIndex][0],
            landmarks[secondJointIndex][1]
          );

          ctx.strokeStyle = "gold";
          ctx.lineWidth = 4;

          ctx.stroke();
        }
      }

      // Draw landmarks
      for (let i = 0; i < landmarks.length; i++) {
        const x = landmarks[i][0];
        const y = landmarks[i][1];

        ctx.beginPath();

        ctx.arc(
          x,
          y,
          style[i].size,
          0,
          2 * Math.PI
        );

        ctx.fillStyle = style[i].color;

        ctx.fill();
      }
    });
  }
};
```

---

# 🖥️ Webcam + Canvas

The project uses two layers:

```text
┌─────────────────────────────┐
│          Canvas             │
│    ✦ Hand landmarks ✦      │
│                             │
│          ✋                 │
│                             │
├─────────────────────────────┤
│          Webcam             │
│       Camera Feed           │
└─────────────────────────────┘
```

The **Webcam** provides the video.

The **Canvas** is placed over the video and is used to draw the detected landmarks.

The two React refs are used to access these elements:

```javascript
const webcamRef = useRef(null);
const canvasRef = useRef(null);
```

---

# 📁 Suggested Project Structure

```text
GestureRecognition/
│
├── public/
│
├── src/
│   ├── assets/
│   │   ├── victory.jpeg
│   │   └── thumbs_up.jpeg
│   │
│   ├── components/
│   │
│   ├── App.jsx
│   ├── App.css
│   ├── index.css
│   ├── main.jsx
│   │
│   └── utilities/
│       └── drawHand.js
│
├── index.html
├── package.json
├── vite.config.js
└── README.md
```

---

# 🔮 Future Improvements

The project can be extended into a complete gesture recognition application.

### Planned Features

* [ ] Real-time hand detection
* [ ] Draw 21 hand landmarks
* [ ] Recognize thumbs-up gesture
* [ ] Recognize victory gesture
* [ ] Add custom gestures
* [ ] Display detected gesture on screen
* [ ] Add gesture confidence score
* [ ] Support multiple hands
* [ ] Improve canvas rendering
* [ ] Add gesture-based UI controls
* [ ] Add more Infinite Gauntlet-style effects

---

# 🎯 Learning Goals

This project is useful for learning how several technologies work together:

```text
React
  │
  ├── Components
  ├── useRef
  ├── useState
  └── useEffect
        │
        ▼
React Webcam
        │
        ▼
TensorFlow.js
        │
        ▼
Computer Vision
        │
        ▼
Handpose
        │
        ▼
Hand Landmarks
        │
        ▼
Canvas API
        │
        ▼
Fingerpose
        │
        ▼
Gesture Recognition
```

The main goal is to understand how **computer vision can be combined with a React application to create real-time interactive experiences**.

---

# 🙌 Inspiration

This project is inspired by real-time hand gesture recognition projects using TensorFlow.js and the Handpose model.

The Infinite Gauntlet visual style is used to make the hand landmark visualization more visually interesting and interactive.

---

## 📜 License

This project is intended for learning and experimentation.

If you use code or ideas from another repository, review and comply with that project's original license.

```
```
