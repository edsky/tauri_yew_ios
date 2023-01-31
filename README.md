# tauri_yew_ios
An app compiled by tauri+yew running on ios

# create

Generate a demo

```
cargo install create-tauri-app
cargo create-tauri-app

✔ Project name · tauri-demo
✔ Choose your package manager · cargo
✔ Choose your UI template · yew
```

# ios support

```
cd tauri-demo && cd src-tauri
cargo add tauri@2.0.0-alpha.0
cargo add tauri-build@2.0.0-alpha.0 --build
cargo install tauri-cli --version "^2.0.0-alpha"
```

> https://tauri.app/blog/2022/12/09/tauri-mobile-alpha/

# patch

create `lib.rs` in `tauri-demo/src-tauri/src`

```rust
#![cfg_attr(
    all(not(debug_assertions), target_os = "windows"),
    windows_subsystem = "windows"
)]

// Learn more about Tauri commands at https://tauri.app/v1/guides/features/command
#[tauri::command]
fn greet(name: &str) -> String {
    format!("Hello, {}! You've been greeted from Rust!", name)
}

#[no_mangle]
pub extern "C"
fn start_app() {
    tauri::Builder::default()
        .invoke_handler(tauri::generate_handler![greet])
        .run(tauri::generate_context!())
        .expect("error while running tauri application");
}
```

change `identifier` in `tauri-demo/src-tauri/tauri.conf.json`

change `IPHONEOS_DEPLOYMENT_TARGET` in `tauri-demo/src-tauri/gen/apple/tauri-demo.xcodeproj/project.pbxproj`

# patch for dev

change `address = "127.0.0.1"` to `address = "0.0.0.0"` in `tauri-demo/Trunk.toml`

# compile

run:

```
cd tauri-demo
cargo tauri ios init
cargo tauri ios build
or
cagro tauri ios dev
```

# Troubleshooting

## 1. ios init failed

use brew to install `cocoapods`

use rvm install ruby

```
brew uninstall ruby
curl -L https://get.rvm.io | bash -s stable --rails
```

> https://www.jianshu.com/p/36a7a354d10f
> https://stackoverflow.com/questions/31972968/cant-install-gems-on-os-x-el-capitan/33043199#33043199

## 2. GCC_PREPROCESSOR_DEFINITIONS: parameter null or not set

look `XCode` and add Release to `DEBUG=0`

> From: `https://github.com/tauri-apps/tauri/issues/5811#issuecomment-1407578660`

## 3. Xcode Command /usr/bin/codesign failed with exit code 1 : errSecInternalComponent

security unlock-keychain login.keychain

## 4. Application info provider (FBSApplicationLibrary) returned nil for "com.tauri.tauri-demo"

reboot simulator