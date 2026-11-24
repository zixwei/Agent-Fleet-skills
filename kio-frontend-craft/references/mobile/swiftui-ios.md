# SwiftUI & iOS Native Interop

## 1. SwiftUI Component Architecture
When building iOS-specific features (Live Activities, Lock Screen Widgets, or native views):
```swift
import SwiftUI

struct KioMetricCard: View {
    let title: String
    let value: String
    let delta: Double
    
    var body: some View {
        VStack(alignment: .leading, spacing: 8) {
            Text(title)
                .font(.subheadline)
                .foregroundColor(.secondary)
            
            Text(value)
                .font(.system(size: 28, weight: .bold, design: .rounded))
            
            HStack(spacing: 4) {
                Image(systemName: delta >= 0 ? "arrow.up.right" : "arrow.down.right")
                Text(String(format: "%.1f%%", abs(delta)))
            }
            .font(.caption.weight(.semibold))
            .foregroundColor(delta >= 0 ? .green : .red)
        }
        .padding(16)
        .background(Color(.secondarySystemBackground))
        .cornerRadius(16)
    }
}
```

## 2. Expo Custom Native Module Pattern
Create high-performance bridges without touching legacy Obj-C:
```swift
import ExpoModulesCore

public class KioHapticsModule: Module {
  public func definition() -> ModuleDefinition {
    Name("KioHaptics")

    AsyncFunction("triggerSuccessImpact") { () -> Void in
      DispatchQueue.main.async {
        let generator = UINotificationFeedbackGenerator()
        generator.notificationOccurred(.success)
      }
    }
  }
}
```
