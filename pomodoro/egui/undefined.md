# 설치

egui를 사용하려면 렌더링 백엔드가 필요합니다. 렌더링 백엔드는 몇 가지가 있습니다.&#x20;

* eframe
* bevy\_egui
* egui\_sdl2\_gl

eframe이 egui 소스에 포함된 백엔드라 eframe으로 시작합니다.&#x20;

eframe은 [템플릿 리포](https://github.com/emilk/eframe\_template/)에서 프로젝트 템플릿을 제공합니다.&#x20;

템플릿 리포에 있는 Cargo.toml의 dependency 이하 항목을 복사해서 가져옵니다.&#x20;

```toml
[package]
name = "realegui"
version = "0.1.0"
edition = "2021"


[dependencies]
egui = "0.21.0"
eframe = { version = "0.21.0", default-features = false, features = [
    "accesskit",     # Make egui comptaible with screen readers. NOTE: adds a lot of dependencies.
    "default_fonts", # Embed the default egui fonts.
    "glow",          # Use the glow rendering backend. Alternative: "wgpu".
    "persistence",   # Enable restoring app state when restarting the app.
] }

# You only need serde if you want app persistence:
serde = { version = "1", features = ["derive"] }

# native:
[target.'cfg(not(target_arch = "wasm32"))'.dependencies]
tracing-subscriber = "0.3"

# web:
[target.'cfg(target_arch = "wasm32")'.dependencies]
console_error_panic_hook = "0.1.6"
tracing-wasm = "0.2"
wasm-bindgen-futures = "0.4"


[profile.release]
opt-level = 2 # fast and small wasm

# Optimize all dependencies even in debug builds:
[profile.dev.package."*"]
opt-level = 2

[patch.crates-io]
```

```bash
cargo new realegui
cargo build
```

