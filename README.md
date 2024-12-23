<details>
<summary><code><strong>BPB 点击展开</strong></code></summary>
<Br>
<details>
<summary><code><strong>点击展开</strong></code></summary>

```YAML
name: BPB-构建和部署 Worker
#name: Build and Deploy Worker

on:
  push:
#    branches: # 分支更新运行
#      - main
    paths:
      - 'BPB/src/**' # 仅当 src 文件夹下的文件发生变化时触发
  workflow_dispatch: # 允许手动触发工作流

permissions:
  contents: write

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Check out the code
        uses: actions/checkout@v4

      - name: Set up Node.js
        uses: actions/setup-node@v4
        with:
          node-version: "latest"

      - name: Install dependencies
        run: npm install

      - name: Build project
        run: |
          npx wrangler deploy src/worker.js --name bpb-worker-panel --compatibility-flag [nodejs_compat] --compatibility-date 2024-10-26 --dry-run --outdir=dist
          npx javascript-obfuscator dist/worker.js --output _worker_BPB.js \
            --compact true \
            --control-flow-flattening true \
            --control-flow-flattening-threshold 0.5 \
            --dead-code-injection false \
            --identifier-names-generator hexadecimal \
            --rename-globals true \
            --string-array true \
            --string-array-encoding 'rc4' \
            --string-array-threshold 0.5 \
            --transform-object-keys true \
            --unicode-escape-sequence true

      - name: Commit and push built worker
        run: |
          git config --global user.name "github-actions[bot]"
          git config --global user.email "github-actions[bot]@users.noreply.github.com"
          git add _worker_BPB.js
          git commit -m "Automated build: update _worker_BPB.js"
          git push
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```
</details>
<br>
<details>
<summary><code><strong>点击展开 文件体积可能会过大</strong></code></summary>

```YAML
name: BPB-构建和部署 Worker
#name: Build and Deploy Worker

on:
  push:
#    branches: # 分支更新运行
#      - main
    paths:
      - 'BPB/src/**' # 仅当 src 文件夹下的文件发生变化时触发
  workflow_dispatch: # 允许手动触发工作流

permissions:
  contents: write

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Check out the code
        uses: actions/checkout@v4

      - name: Set up Node.js
        uses: actions/setup-node@v4
        with:
          node-version: "latest"

      - name: Install dependencies
        run: npm install

      - name: Build project
        run: |
          npx wrangler deploy src/worker.js --name bpb-worker-panel --compatibility-flag [nodejs_compat] --compatibility-date 2024-10-26 --dry-run --outdir=dist
          npx javascript-obfuscator dist/worker.js --output _worker_BPB.js \
            --compact true \
            --control-flow-flattening true \
            --control-flow-flattening-threshold 1 \
            --dead-code-injection true \
            --dead-code-injection-threshold 1 \
            --identifier-names-generator hexadecimal \
            --rename-globals true \
            --string-array true \
            --string-array-encoding 'rc4' \
            --string-array-threshold 1 \
            --transform-object-keys true \
            --unicode-escape-sequence true

      - name: Commit and push built worker
        run: |
          git config --global user.name "github-actions[bot]"
          git config --global user.email "github-actions[bot]@users.noreply.github.com"
          git add _worker_BPB.js
          git commit -m "Automated build: update _worker_BPB.js"
          git push
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```
</details>
</details>
<br><br><br><br><br>
<details>
<summary><code><strong>edgetunnel 点击展开</strong></code></summary>

```YAML
name: edgetunnel-混淆并提交
# name: Obfuscate and Commit

on:
    #push:
    #    paths:
    #        - 'edgetunnel/明文源码.js'
    workflow_dispatch:

jobs:
  obfuscate:
    runs-on: ubuntu-latest
    permissions:
      contents: write

    steps:
      - uses: actions/checkout@v3
        with:
          fetch-depth: 0

      - name: Use Node.js
        uses: actions/setup-node@v3
        with:
          node-version: "16"

      - name: Install dependencies
        run: npm install -g javascript-obfuscator

      - name: Obfuscate code
        run: |
          javascript-obfuscator edgetunnel/明文源码.js --output _worker_edgetunnel.js \
          --compact true \
          --control-flow-flattening true \
          --control-flow-flattening-threshold 1 \
          --dead-code-injection true \
          --dead-code-injection-threshold 1 \
          --identifier-names-generator hexadecimal \
          --rename-globals true \
          --string-array true \
          --string-array-encoding 'rc4' \
          --string-array-threshold 1 \
          --transform-object-keys true \
          --unicode-escape-sequence true

      - name: Commit changes
        run: |
          git config --local user.email "github-actions[bot]@users.noreply.github.com"
          git config --local user.name "github-actions[bot]"
          git add _worker_edgetunnel.js
          git commit -m "Obfuscate _worker_edgetunnel.js" || echo "No changes to commit"

      - name: Push changes
        uses: ad-m/github-push-action@master
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          branch: ${{ github.ref }}
```
</details>

<br><br><br><br><br>
<details>
<summary><code><strong><br>增加更多的混淆方法 并进行注释 点击展开<br>太离谱了，没加，如果需要的话自己加</strong></code></summary>

```YAML
--compact true                                         # (移除多余的空白和注释，压缩代码, 默认: true, 可选值: true/false)
--control-flow-flattening true                         # (启用控制流平坦化，重构代码逻辑结构, 默认: false, 可选值: true/false)
--control-flow-flattening-threshold 0.75               # (控制流平坦化应用比例, 默认: 0.75, 可选值: 0 ~ 1.0)
--dead-code-injection true                             # (注入伪造的死代码，增加代码复杂性, 默认: false, 可选值: true/false)
--dead-code-injection-threshold 0.4                    # (死代码注入比例, 默认: 0.4, 可选值: 0 ~ 1.0)
--identifier-names-generator hexadecimal               # (混淆变量和函数名, 默认: mangled, 可选值: hexadecimal/mangled/dictionary)
--rename-globals true                                  # (重命名全局变量，防止通过名字推测用途, 默认: false, 可选值: true/false)
--string-array true                                    # (将字符串替换为数组引用，提高混淆难度, 默认: true, 可选值: true/false)
--string-array-encoding 'base64'                       # (字符串数组编码方式, 默认: none, 可选值: base64/rc4/none)
--string-array-index-shift true                        # (启用字符串数组索引偏移，防止直接访问, 默认: false, 可选值: true/false)
--string-array-threshold 0.75                          # (字符串数组应用比例, 默认: 0.75, 可选值: 0 ~ 1.0)
--string-array-wrappers-chained-calls true             # (链式调用字符串数组包装器，增加复杂度, 默认: false, 可选值: true/false)
--string-array-wrappers-count 5                        # (字符串数组包装器数量, 默认: 1, 可选值: 1 ~ 5)
--string-array-wrappers-parameters-max-count 4         # (字符串包装器的最大参数数量, 默认: 2, 可选值: 1 ~ 4)
--transform-object-keys true                           # (转换对象键名为混淆形式, 默认: false, 可选值: true/false)
--unicode-escape-sequence true                         # (将字符串转换为 Unicode 转义序列, 默认: false, 可选值: true/false)
--split-strings true                                   # (将字符串拆分为多个部分，增加混淆难度, 默认: false, 可选值: true/false)
--split-strings-chunk-length 10                        # (字符串拆分的块长度, 默认: 10, 可选值: 0.1 ~ 10)
--disable-console-output true                          # (禁用控制台输出(如 console.log), 默认: false, 可选值: true/false)
--debug-protection true                                # (添加调试保护，防止通过 DevTools 调试, 默认: false, 可选值: true/false)
--debug-protection-interval true                       # (启用调试保护间隔检测, 默认: false, 可选值: true/false)
--debug-protection-interval 1000                       # (调试保护间隔时间，单位为毫秒, 默认: 0, 可选值: 0 ~ 无最大值)
--self-defending true                                  # (启用自我防护，防止代码被美化或修改, 默认: false, 可选值: true/false)

```

</details>
