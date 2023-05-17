# hello world par

hello world의 parallel 버전입니다. thread와 channel을 사용합니다.&#x20;

```toml
[[bin]]
name = "hello-world-par"
path = "src/hello_world_par.rs"
```

* \[\[bin]]을 Cargo.toml에추가하고 hello\_world\_par.rs를 만듭니다.&#x20;
* hello\_world\_par.rs에 examples의 코드를 복사해서 붙여넣습니다.

```bash
cargo build --bin hello-world-par
cargo run --bin hello-world-par
```

* 멀티쓰레드로 여러 개의 패널을 실행합니다.&#x20;
* 이벤트 처리도 잘 됩니다.&#x20;
* 러스트는 대단합니다.

## 코드 분석

{% code lineNumbers="true" %}
```rust
fn new_worker(
    thread_nr: usize,
    on_done_tx: mpsc::SyncSender<()>,
) -> (JoinHandle<()>, mpsc::SyncSender<egui::Context>) {
    let (show_tx, show_rc) = mpsc::sync_channel(0);
    let handle = std::thread::Builder::new()
        .name(format!("EguiPanelWorker {}", thread_nr))
        .spawn(move || {
            let mut state = ThreadState::new(thread_nr);
            while let Ok(ctx) = show_rc.recv() {
                state.show(&ctx);
                let _ = on_done_tx.send(());
            }
        })
        .expect("failed to spawn thread");
    (handle, show_tx)
}
```
{% endcode %}

* 쓰레드를 생성해서 ThreadState 클래스를 실행합니다.&#x20;
* 채널을 통해 update()를 하라고 show\_rc.recv()를 하면 show()를 합니다.&#x20;
* on\_done\_tx.send()를 통해 MyApp::update()가 다음 프레임으로 진행하도록 합니다.

쓰레드 분리를 통해 얻는 이득은 ThreadState::show() 실행 정도입니다. 거의 동기에 가까운 방식으로 처리합니다.&#x20;

<details>

<summary>놀미 노트</summary>

중요한 점은 코드가 명확하고 오류가 없다는 점입니다. 러스트의 관용적인 패턴을 여러 크레이트를 사용하면서 익혀야 합니다.&#x20;

</details>

