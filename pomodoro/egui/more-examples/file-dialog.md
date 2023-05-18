# file dialog

hello world par와 거의 비슷하게 \[\[bin]]을 구성하고 예제 내용을 file\_dialog.rs에 복사한다.&#x20;

빌드하면 rfd 모듈이 없다고 나온다. rfd는 크레이트이다. 의존성에 추가하고 확인한다.&#x20;

이번에는 버전 체크 등을 위해 cargo add rfd로 추가했다.&#x20;

```toml
rfd = "0.11.3"
```

* dependency에 위 라인이 추가되었다.&#x20;

그리고 잘 실행됩니다.&#x20;

<figure><img src="../../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

## 코드 분석

다른 부분은 거의 동일합니다. 아래는 MyApp::udpate() 내의 add\_contents 클로저 내의 드랍 파일들 표시 부분입니다.&#x20;

{% code lineNumbers="true" %}
```rust
  if !self.dropped_files.is_empty() {
        ui.group(|ui| {
            ui.label("Dropped files:");

            for file in &self.dropped_files {
                let mut info = if let Some(path) = &file.path {
                    path.display().to_string()
                } else if !file.name.is_empty() {
                    file.name.clone()
                } else {
                    "???".to_owned()
                };
                if let Some(bytes) = &file.bytes {
                    use std::fmt::Write as _;
                    write!(info, " ({} bytes)", bytes.len()).ok();
                }
                ui.label(info);
            }
        });
    }
```
{% endcode %}

* 라인 6에서 시작하는 if let 부분이 else if를 같이 쓰고 있습니다.&#x20;
* 라인 14에 Write에 접근 불가능하게 하면서 트레이트만 가져오는 기법이 있습니다.&#x20;

```rust
// Collect dropped files:
ctx.input(|i| {
    if !i.raw.dropped_files.is_empty() {
        self.dropped_files = i.raw.dropped_files.clone();
    }
});
```

* `i`는 `&InputState`입니다. 여기서 `dropped_files` 벡터를 `clone()`해서 가져옵니다.&#x20;

