# vendor ── 同梱している外部ライブラリ

外部CDNから読まずに、ここに置いたものを配る。理由は2つ。

1. **可用性** ── CDNが落ちても、ページが半分だけ壊れた状態にならない。
   全部出るか、サイトごと開かないかのどちらかになる。地域による到達性の問題も消える。
2. **改ざん対策** ── `importmap` 経由のES Modulesには SRI（改ざん検知）が素直に付かない。
   同梱以外に、配られる中身を固定する手がない。

## three.js 0.160.0

- 取得元: `https://cdn.jsdelivr.net/npm/three@0.160.0/`
- 取得日: 2026-08-01
- 16ファイル / 1.31 MB
- 入口から相対importを再帰的にたどって収集（手作業で取りこぼさないため）

使っているページと、それぞれの入口。

| ページ | 読み込むモジュール |
|---|---|
| `index.html` | three, OrbitControls, CSS2DRenderer |
| `road.html` | three, OrbitControls, CSS2DRenderer |
| `arch.html` | three, OrbitControls, CSS2DRenderer, RoomEnvironment |
| `aimap/index.html` | three, OrbitControls, BufferGeometryUtils, EffectComposer, RenderPass, ShaderPass, OutputPass, UnrealBloomPass |
| `mirai/index.html` | three, RoundedBoxGeometry（**動的import**。静的なgrepでは見つからない） |

`importmap` の参照先はページの階層に合わせた相対パス（`./vendor/…` と `../vendor/…`）。
絶対パスにするとローカルで開いた時に壊れる。

## 更新するとき

1. バージョンを決める（`@latest` は使わない）
2. 上記の入口から相対importを再帰収集して差し替える
3. **5ページすべてを実際に描画して確認する** ── WebGLのcanvasが生きているか、
   コンソールエラーが無いか、外部への通信が発生していないか
