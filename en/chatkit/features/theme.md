> ## Documentation Index
> Fetch the complete documentation index at: https://raw.githubusercontent.com/Tencent-RTC/docs/main/en/llms.txt
> Use this file to discover all available pages before exploring further.

**Applicable Platforms: Android, iOS, Flutter**

TUIKit Compose provides powerful appearance customization capabilities, supporting custom theme modes (Light/Dark/Follow System) and theme colors for your application. Through simple configuration, developers can easily implement app-level theme switching and brand color customization.

Taking the message list as an example, the effects of custom theme modes and theme colors are shown below:
| **Light + Default Color** | **Light + Orange** | **Dark + Default Color** | **Dark + Green Theme** |
| --- | --- | --- | --- |
|  |  |  |  |

## API Reference
The above functionalities are based on a set of concise APIs provided by `ThemeState` for theme configuration and information queries.

### Set Theme Mode
| Method | Parameters | Return Value | Description |
| --- | --- | --- | --- |
| `setThemeMode` | `ThemeMode` | `Unit` | Sets the display mode, automatically saves and refreshes. • `ThemeMode.SYSTEM`: Follow system appearance. • `ThemeMode.LIGHT`: Force light mode. • `ThemeMode.DARK`: Force dark mode. |

### Set Theme Color
| Method | Parameters | Return Value | Description |
| --- | --- | --- | --- |
| `setPrimaryColor` | `String` | `Unit` | Sets the theme color, requires Hex format (e.g., "#1C66E5"). |
| `clearPrimaryColor` | None | `Unit` | Clears custom theme color and restores default blue. |

> **Note:**
> - After calling `setThemeMode` or `setPrimaryColor`, the configuration is automatically saved to persistent storage without manual saving. It will automatically load on next app launch.
> - Cache is automatically cleared when modifying theme configuration, ensuring the color scheme updates in real-time. Developers don't need to manage cache.
> - Avoid frequently switching themes when using settings APIs (e.g., calling in a loop).

### Query ThemeState Information

The following read-only properties can be used to query the current theme configuration status and information:
| Property | Type | Description |
| --- | --- | --- |
| `currentMode` | `ThemeMode` | Current theme mode. |
| `currentPrimaryColor` | `String?` | Current theme color (Hex format). |
| `hasCustomPrimaryColor` | `Boolean` | Whether a custom theme color is set. |
| `isDarkMode` | `Boolean` | Whether currently in dark mode. |

## Platform: Android

## Core Capabilities

**Mode Switching**
- Light Mode: Bright interface suitable for daytime use.

- Dark Mode: Dark interface suitable for nighttime use.

- Follow System: Automatically follows system appearance settings.

   **Theme Color Customization**

- Preset Theme Colors: Provides standard colors like blue, green, red, orange, etc.

- Custom Theme Colors: Supports any Hex format color.

- Smart Palette Generation: Automatically generates a complete palette of 10 color shades.

- Clear Customization: One-click restore to default theme color.

## Usage

This section introduces how to integrate and use theme and theme color customization features in your application, providing three approaches from simple to complex.

### Approach 1: Using AppBuilder Configuration (Recommended)

AppBuilder is a convenient feature and UI customization configuration tool, managed through JSON configuration files, suitable for scenarios where Web + Native dual-platform applications need to maintain a unified appearance.

You can experience the AppBuilder feature at [Chat Web Demo](https://trtc.io/demo/homepage/#/detail?scene=messenger), experience entry:

Although the Chat Web Demo currently cannot preview mobile configuration effects in real-time, mobile platforms already support AppBuilder's configuration capabilities.

Next, we'll introduce how to apply custom configurations to mobile projects after setting up AppBuilder in the Web demo. The process is quite simple, with only 2 steps:
1. Download the configuration file.

2. Drag the configuration file appConfig.json into your Android project.

####  1. Download Configuration File

Visit [Chat Web Demo](https://trtc.io/demo/homepage/#/detail?scene=messenger) and download the appConfig.json configuration file. The download path is:

The `appConfig.json` file content is shown below, corresponding one-to-one with the configuration items on the AppBuilder page. The appearance-related configurations are `theme` and its subfields:
``` json
{
  "theme": {
    "mode": "light", // Theme mode: light-Light mode, dark-Dark mode, system-Follow system
    "primaryColor": "#E65100" // Primary color, hex color value
  },
  "messageList": {
    "alignment": "two-sided",
    "enableReadReceipt": false,
    "messageActionList": [
        "copy",
        "recall",
        "quote",
        "forward",
        "delete"
    ]
  },
  "conversationList": {
    "enableCreateConversation": true,
    "conversationActionList": [
      "delete",
      "mute",
      "pin",
      "markUnread"
    ]
  },
  "messageInput": {
    "hideSendButton": false,
    "attachmentPickerMode": "collapsed"
  },
  "search": {
    "hideSearch": false
  },
  "avatar": {
    "shape": "circular"
  }
}
```

**Configuration Options**
| Parameter | Type | Options | Description |
| --- | --- | --- | --- |
| `mode` | String | `"system"`, `"light"`, `"dark"` | Theme mode |
| `primaryColor` | String | Hex color value, e.g., `"#0ABF77"` | Theme color |

#### 2. Add Configuration File to Project

Drag appConfig.json into your project. Taking the GitHub open source Demo [TUIKit Android Compose](https://github.com/Tencent-RTC/TUIKit_Android_Compose/blob/main/chat/demo/app/src/main/assets/appConfig.json) project as an example, appConfig.json is located in the demo/app's assets folder:

After this, without writing additional code, when you launch the app, the mode and theme color of TUIKit Compose components will automatically display the values you set in appConfig.json.

### Approach 2: Set Theme Mode via Code

You can directly set the theme mode through the API `setThemeMode`. Example code:
``` swift
val themeState = ThemeState.shared
Column {
    Button(onClick = {
        themeState.setThemeMode(ThemeMode.SYSTEM)
    }) { Text(text = "Follow System") }
    Button(onClick = {
        themeState.setThemeMode(ThemeMode.LIGHT)
    }) { Text(text = "Light Mode") }
    Button(onClick = {
        themeState.setThemeMode(ThemeMode.DARK)
    }) { Text(text = "Dark Mode") }
}
```

### Approach 3: Set Theme Color via Code

You can set the theme color through the API `setPrimaryColor` by passing a primary color value. The entire TUIKit Compose component will automatically adapt a set of similar color values in the same color scheme based on this primary color value to render the interface, without the need for manual UI adaptation. Example code:
``` swift
Column {
    var text: String by remember { mutableStateOf("#1C66E5") }

    fun isValidHexColor(): Boolean {
        return text.matches(Regex("^#[0-9A-Fa-f]{6}$"))
    }
    BasicTextField(
        value = text,
        onValueChange = { text = it },
        modifier = Modifier
            .fillMaxWidth()
            .padding(16.dp)
    )
    Button(
        enabled = isValidHexColor(),
        onClick = {
            themeState.setPrimaryColor(text)
        }) {
        Text("Apply Theme Color")
    }
}
```

> **Usage Recommendations:**
> - For most applications, it's recommended to use the JSON configuration file approach, which is simple, reliable, and supports multi-platform uniformity.
> - Consistently use colors from `themeState.colors` throughout the application to ensure interface consistency when switching themes.

## Platform: iOS

## Core Capabilities

**Mode Switching**
- Light Mode: Bright interface suitable for daytime use.

- Dark Mode: Dark interface suitable for nighttime use.

- Follow System: Automatically follows system appearance settings.

   **Theme Color Customization**

- Preset Theme Colors: Provides standard colors like blue, green, red, orange, etc.

- Custom Theme Colors: Supports any Hex format color.

- Smart Palette Generation: Automatically generates a complete palette of 10 color shades.

- Clear Customization: One-click restore to default theme color.

## Usage

This section introduces how to integrate and use theme and theme color customization features in your application, providing three approaches from simple to complex.

### Approach 1: Using AppBuilder Configuration (Recommended)

AppBuilder is a convenient feature and UI customization configuration tool, managed through JSON configuration files, suitable for scenarios where Web + Native dual-platform applications need to maintain a unified appearance.

You can experience the AppBuilder feature at [Chat Web Demo](https://trtc.io/demo/homepage/#/detail?scene=messenger), experience entry:

Although the Chat Web Demo currently cannot preview mobile configuration effects in real-time, mobile platforms already support AppBuilder's configuration capabilities.

Next, we'll introduce how to apply custom configurations to mobile projects after setting up AppBuilder in the Web demo. The process is quite simple, with only 3 steps:
1. Download the configuration file.

2. Drag the configuration file appConfig.json into your iOS project.

3. Load the configuration file at app launch.

#### Step 1: Download Configuration File

Visit [Chat Web Demo](https://trtc.io/demo/homepage/#/detail?scene=messenger) and download the appConfig.json configuration file. The download path is:

The `appConfig.json` file content is shown below, corresponding one-to-one with the configuration items on the AppBuilder page. The appearance-related configurations are `theme` and its subfields:
``` json
{
  "theme": {
    "mode": "light", // Theme mode: light-Light mode, dark-Dark mode, system-Follow system
    "primaryColor": "#E65100" // Primary color, hex color value
  },
  "messageList": {
    "alignment": "two-sided",
    "enableReadReceipt": false,
    "messageActionList": [
        "copy",
        "recall",
        "quote",
        "forward",
        "delete"
    ]
  },
  "conversationList": {
    "enableCreateConversation": true,
    "conversationActionList": [
      "delete",
      "mute",
      "pin",
      "markUnread"
    ]
  },
  "messageInput": {
    "hideSendButton": false,
    "attachmentPickerMode": "collapsed"
  },
  "search": {
    "hideSearch": false
  },
  "avatar": {
    "shape": "circular"
  }
}
```

**Configuration Options**
| Parameter | Type | Options | Description |
| --- | --- | --- | --- |
| `mode` | String | `"system"`, `"light"`, `"dark"` | Theme mode. |
| `primaryColor` | String | Hex color value, e.g., `"#0ABF77"` | Theme color. |

#### Step 2: Add Configuration File to Project

Drag appConfig.json into your project, ensuring the correct Target is selected and Copy Bundle Resources is properly checked.

Taking the GitHub open source Demo [TUIKit iOS SwiftUI](https://github.com/Tencent-RTC/TUIKit_iOS_SwiftUI/blob/main/chat/demo/ChatDemo/appConfig.json) project as an example, appConfig.json is located in the main bundle of demo/ChatDemo:

#### Step 3: Load Configuration File

Set the JSON file path at app launch to load the configuration file. If you also place appConfig.json in the app's main bundle, you can obtain the JSON file like this:
``` swift
import AtomicX

@main
struct MyApp: App {
    init() {
        // Load theme configuration file
        if let path = Bundle.main.path(forResource: "appConfig", ofType: "json") {
            AppBuilderHelper.setJsonPath(path: path)
        }
    }
    
    var body: some Scene {
        WindowGroup {
            ContentView()
                // Inject ThemeState.shared into the app's root view
                .environmentObject(ThemeState.shared)
        }
    }
}
```

After this, without writing additional code, when you launch the app, the mode and theme color of TUIKit SwiftUI components will automatically display the values you set in appConfig.json.

### Approach 2: Set Theme Mode via Code

You can directly set the theme mode through the API `setThemeMode`, where themeState is the ThemeState.shared singleton object obtained from the environment. Example code:
``` swift
import SwiftUI
import AtomicX

struct SettingsView: View {
    @EnvironmentObject var themeState: ThemeState
    
    var body: some View {
        List {
            Section("Appearance Mode") {
                Button("Follow System") {
                    themeState.setThemeMode(.system)
                }
                Button("Light Mode") {
                    themeState.setThemeMode(.light)
                }
                Button("Dark Mode") {
                    themeState.setThemeMode(.dark)
                }
            }
        }
        .navigationTitle("Settings")
    }
}
```

### Approach 3: Set Theme Color via Code

You can set the theme color through the API `setPrimaryColor` by passing a primary color value. The entire TUIKit SwiftUI component will automatically adapt a set of similar color values in the same color scheme based on this primary color value to render the interface, without the need for manual UI adaptation. Example code:
``` swift
struct CustomColorPicker: View {
    @EnvironmentObject var themeState: ThemeState
    @State private var customColor = "#1C66E5"
    
    var body: some View {
        VStack(spacing: 20) {
            TextField("Enter color value", text: $customColor)
                .textFieldStyle(RoundedBorderTextFieldStyle())
                .placeholder(when: customColor.isEmpty) {
                    Text("#1C66E5").foregroundColor(.gray)
                }
            
            Button("Apply Theme Color") {
                themeState.setPrimaryColor(customColor)
            }
            .disabled(!isValidHexColor(customColor))
        }
        .padding()
    }
    
    private func isValidHexColor(_ hex: String) -> Bool {
        let pattern = "^#[0-9A-Fa-f]{6}$"
        return hex.range(of: pattern, options: .regularExpression) != nil
    }
}
```

> **Usage Recommendations:**
> - For most applications, it's recommended to use the JSON configuration file approach, which is simple, reliable, and supports multi-platform uniformity.
> - Consistently use colors from `themeState.colors` throughout the application to ensure interface consistency when switching themes.

## Platform: Flutter

## Core Capabilities

**Mode Switching**
- Light Mode: Bright interface suitable for daytime use.

- Dark Mode: Dark interface suitable for nighttime use.

- Follow System: Automatically follows system appearance settings.

   **Theme Color Customization**

- Preset Theme Colors: Provides standard colors like blue, green, red, orange, etc.

- Custom Theme Colors: Supports any Hex format color.

- Smart Palette Generation: Automatically generates a complete palette of 10 color shades.

- Clear Customization: One-click restore to default theme color.

## Usage

This section introduces how to integrate and use theme and theme color customization features in your application, providing three approaches from simple to complex.

### Approach 1: Using AppBuilder Configuration (Recommended)

AppBuilder is a convenient feature and UI customization configuration tool, managed through JSON configuration files, suitable for scenarios where Web + Native dual-platform applications need to maintain a unified appearance.

You can experience the AppBuilder feature at [Chat Web Demo](https://trtc.io/demo/homepage/#/detail?scene=messenger), experience entry:

Although the Chat Web Demo currently cannot preview mobile configuration effects in real-time, mobile platforms already support AppBuilder's configuration capabilities.

Next, we'll introduce how to apply custom configurations to mobile projects after setting up AppBuilder in the Web demo. The process is quite simple, with only 2 steps:
1. Download the configuration file.

2. Drag the configuration file appConfig.json into your Flutter project.

3. Load the configuration file when the app starts.

#### Step 1: Download Configuration File

Visit [Chat Web Demo](https://trtc.io/demo/homepage/#/detail?scene=messenger) and download the appConfig.json configuration file. The download path is:

The `appConfig.json` file content is shown below, corresponding one-to-one with the configuration items on the AppBuilder page. The appearance-related configurations are `theme` and its subfields:
``` json
{
  "theme": {
    "mode": "light", // Theme mode: light-Light mode, dark-Dark mode, system-Follow system
    "primaryColor": "#E65100" // Primary color, hex color value
  },
  "messageList": {
    "alignment": "two-sided",
    "enableReadReceipt": false,
    "messageActionList": [
        "copy",
        "recall",
        "quote",
        "forward",
        "delete"
    ]
  },
  "conversationList": {
    "enableCreateConversation": true,
    "conversationActionList": [
      "delete",
      "mute",
      "pin",
      "markUnread"
    ]
  },
  "messageInput": {
    "hideSendButton": false,
    "attachmentPickerMode": "collapsed"
  },
  "search": {
    "hideSearch": false
  },
  "avatar": {
    "shape": "circular"
  }
}
```

**Configuration Options**
| Parameter | Type | Options | Description |
| --- | --- | --- | --- |
| `mode` | String | `"system"`, `"light"`, `"dark"` | Theme mode |
| `primaryColor` | String | Hex color value, e.g., `"#0ABF77"` | Theme color |

#### Step 2: Add Configuration File to Project

Drag appConfig.json into your project. Taking the GitHub open source Demo [TUIKit Flutter](https://github.com/Tencent-RTC/TUIKit_Flutter/tree/main) project as an example, appConfig.json is located in the demo's assets folder:

And configure assets resources in the project's pubspec.yaml file:
``` java
assets:
  - assets/
```

#### Step 3: Load Configuration File

Please set the JSON file path and load the configuration file when your app starts.
``` java
class _MyAppState extends State<MyApp> {
  bool _isInitialized = false;

  @override
  void initState() {
    super.initState();
    _initializeApp();
  }

  Future<void> _initializeApp() async {
    await StorageUtil.init();
    await AppBuilder.init(configPath: 'packages/uikit_next/assets/appConfig.json');

    if (mounted) {
      setState(() {
        _isInitialized = true;
      });
    }
  }
}
```

After this, when you launch the app, the mode and theme color of TUIKit Flutter components will automatically display the values you set in appConfig.json.

### Approach 2: Set Theme Mode via Code

You can directly set the theme mode through the API `setThemeMode`. Example code:
``` swift
val themeState = ThemeState.shared
Column {
    Button(onClick = {
        themeState.setThemeMode(ThemeMode.SYSTEM)
    }) { Text(text = "Follow System") }
    Button(onClick = {
        themeState.setThemeMode(ThemeMode.LIGHT)
    }) { Text(text = "Light Mode") }
    Button(onClick = {
        themeState.setThemeMode(ThemeMode.DARK)
    }) { Text(text = "Dark Mode") }
}
```

### Approach 3: Set Theme Color via Code

You can set the theme color through the API `setPrimaryColor` by passing a primary color value. The entire TUIKit Flutter component will automatically adapt a set of similar color values in the same color scheme based on this primary color value to render the interface, without the need for manual UI adaptation. Example code:
``` swift
final themeState = BaseThemeProvider.of(context);
themeState.setPrimaryColor('#1C66E5');
```

> **Usage Recommendations:**
> - For most applications, it's recommended to use the JSON configuration file approach, which is simple, reliable, and supports multi-platform uniformity.
> - Consistently use colors from `themeState.colors` throughout the application to ensure interface consistency when switching themes.

## FAQs

### **How to determine if currently in dark mode?**

> **Platforms: Android / Flutter**

``` java
> **Android：** if themeState.isDarkMode {
> **Flutter：** if (themeState.isDarkMode) {
    // Currently in dark mode
} else {
    // Currently in light mode
}
```

### **Can I set transparency?**

> **Platforms: Android / Flutter**

Not supported. Theme colors only support 6-digit Hex format (RGB), not 8-digit format (RGBA).

### **How to restore default theme?**

> **Platforms: Android / Flutter**

``` swift
themeState.clearPrimaryColor()  // Clear custom theme color
> **Android：** themeState.setThemeMode(ThemeMode.SYSTEM)  // Restore follow system
> **Flutter：** themeState.setThemeMode(ThemeType.SYSTEM)  // Restore follow system
```

### **Where is theme configuration stored?**

> **Platforms: Android / Flutter**

Stored in the app's persistent storage. It will be lost after uninstalling the app and will restore to default configuration after reinstalling.

### **How to get the complete current theme configuration?**

> **Platforms: Android / Flutter**

**Android：**

``` swift
val config = themeState.currentTheme
print("Mode: ${config.mode}")
print("Theme Color: ${config.primaryColor ?: "Default"}")
```

**Flutter：**

``` swift
final themeState = BaseThemeProvider.of(context);
final config = themeState.currentTheme;
print("Mode: ${config.type}");
print("Theme Color: ${config.primaryColor ?? "Default"}");
```

### **Can I set mode and theme color simultaneously?**

> **Platforms: Android / Flutter**

Yes, you can call both methods separately, and the system will automatically merge the configurations:
``` swift
> **Android：** themeState.setThemeMode(ThemeMode.DARK)
> **Flutter：** themeState.setThemeMode(ThemeType.DARK)
themeState.setPrimaryColor("#0ABF77")
```
