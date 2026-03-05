# JTrackers

A multi-object tracking library for Swift, powered by a Rust-based tracking engine.

Provides three tracking algorithms:

- **ByteTracker** — Two-stage matching using high and low-confidence detections
- **BoostTracker** — IoU + Mahalanobis distance + shape similarity (supports BoostTrack+/++ modes)
- **OCSort** — Observation-centric online smoothing with velocity direction consistency

## Requirements

- macOS 12+
- iOS 15+
- Swift 5.9+

## Installation (Swift Package Manager)

Add the following to your `Package.swift`:

```swift
dependencies: [
    .package(url: "https://github.com/mprg-smilab/JTrackers.git", from: "0.0.2"),
],
targets: [
    .target(
        name: "YourTarget",
        dependencies: ["JTrackers"]
    ),
]
```

In Xcode, go to **File → Add Package Dependencies...** and enter the repository URL above.

## Usage

### Basic Flow

1. Initialize a tracker
2. Pass detections (`[TrackedObject]`) to `update()` for each frame
3. Use the returned `trackId` to track each object across frames

```swift
import JTrackers

// 1. Create a tracker
let tracker = ByteTracker()

// 2. Create detections (e.g. output from an object detection model)
let detections = [
    TrackedObject(x: 10, y: 20, width: 100, height: 200, prob: 0.9),
    TrackedObject(x: 300, y: 400, width: 80, height: 160, prob: 0.8),
]

// 3. Update the tracker and get track IDs
let results = try tracker.update(detections).get()

for obj in results {
    print("Track ID: \(obj.trackId ?? -1), bbox: (\(obj.x), \(obj.y), \(obj.width), \(obj.height))")
}
```

### ByteTracker

```swift
let tracker = ByteTracker(
    frameRate: 30,
    trackBuffer: 30,
    trackThresh: 0.5,
    highThresh: 0.6,
    matchThresh: 0.8
)
```

### BoostTracker

```swift
let tracker = BoostTracker(
    detThresh: 0.5,
    iouThreshold: 0.3,
    maxAge: 30,
    minHits: 3
)

// Enable BoostTrack++ mode
let tracker = BoostTracker(enableBoostPlusPlus: true)
```

### OCSort

```swift
let tracker = OCSort(
    detThresh: 0.5,
    maxAge: 30,
    minHits: 3,
    iouThreshold: 0.3,
    deltaT: 3,
    inertia: 0.2,
    useByte: false
)
```

## Documentation

Full API documentation is available at:
https://kadu-v.github.io/JTrackers/documentation/jtrackers/

## License

See [LICENSE](LICENSE) for details.
