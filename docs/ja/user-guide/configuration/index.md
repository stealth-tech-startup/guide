---
layout: main
title: 全体
category: User Guide
menu: menu_ja
toc:
  - title: Yaml設定
    url: "#yaml設定"
    active: 'true'
---

# Yaml設定

ここではscrewdriver.yamlの主要な設定についてインタラクティブに紹介します。

各プロパティ名にマウスカーソルを乗せるとそれらの説明が表示されます。

<div class="yaml-docs">

<pre class="example">

<a href="#shared"><span class="key">shared</span>:</a>
    <a href="#environment"><span class="key">environment</span>:
    <span class="key">NODE_ENV</span>: <span class="value">test</span></a>
    <a href="#settings"><span class="key">settings</span>:</a>
        <a href="#email"><span class="key">email</span>:
    <span class="key">addresses</span>: <span class="value">[test@email.com, test2@email.com]</span>
    <span class="key">statuses</span>: <span class="value">[SUCCESS, FAILURE]</span></a>
    <a href="#annotations"><span class="key">annotations</span>:
    <span class="key">beta.screwdriver.cd/my-cluster-annotation</span>: <span class="value">my-data</span></a>
        <a href="#executor"><span class="key">beta.screwdriver.cd/executor</span>: <span class="value">k8s-vm</span></a>
        <a href="#cpu"><span class="key">screwdriver.cd/cpu</span>: <span class="value">HIGH</span></a>
        <a href="#ram"><span class="key">screwdriver.cd/ram</span>: <span class="value">LOW</span></a>
<a href="#parameters"><span class="key">parameters</span>:
    <span class="key">region</span>: <span class="value">"us-west-1"</span>
    <span class="key">az</span>: <span class="value">
        <span class="key">value</span>: <span class="value">"zone 1"</span>
        <span class="key">description</span>: <span class="value">"default availability zone"</span></span></a>
<a href="#jobs"><span class="key">jobs</span>:</a>
    <span class="key">main</span>:
        <a href="#requires"><span class="key">requires</span>: <span class="value">[~pr, ~commit, ~sd@123:main]</span></a>
        <a href="#sourcePaths"><span class="key">sourcePaths</span>: <span class="value">["src/app/", "screwdriver.yaml"]</span></a>
        <a href="#image"><span class="key">image</span>: <span class="value">node:lts</span></a>
        <a href="#steps"><span class="key">steps</span>:</a>
            - <span class="key">init</span>: <span class="value">npm install</span>
            - <span class="key">test</span>: <span class="value">npm test</span>
    <span class="key">publish</span>:
        <a href="#requires"><span class="key">requires</span>: <span class="value">[main]</span></a>
        <a href="#template"><span class="key">template</span>: <span class="value">node/publish@4.3.1</span></a>
        <a href="#order"><span class="key">order</span>: <span class="value">[init, publish, teardown-save-results]</span></a>
        <a><span class="key">steps</span>:</a>
            - <a><span class="key">publish</span>: <span class="value">npm install</span></a>
            - <a href="#teardown"><span class="key">teardown-save-results</span>: <span class="value">cp ./results $SD_ARTIFACTS_DIR</span></a>
    <span class="key">deploy-west</span>:
        <a href="#emptyTrigger"><span class="key">requires</span>: <span class="value">[]</span></a>
        <a><span class="key">image</span>: <span class="value">node:lts</span></a>
        <a><span class="key">environment</span>:</a>
            <span class="key">DEPLOY_ENV</span>: <span class="value">west</span>
        <a><span class="key">steps</span>:</a>
            - <span class="key">init</span>: <span class="value">npm install</span>
            - <span class="key">deploy</span>: <span class="value">npm deploy</span>
    <span class="key">deploy-east</span>:
        <span class="key">requires</span>: <span class="value">[deploy-west]</span>
        <span class="key">image</span>: <span class="value">node:lts</span>
        <span class="key">environment</span>:
            <span class="key">DEPLOY_ENV</span>: <span class="value">east</span>
        <span class="key">steps</span>:
            - <span class="key">init</span>: <span class="value">npm install</span>
            - <span class="key">deploy</span>: <span class="value">npm deploy</span>
    <span class="key">finished</span>:
        <a href="#stageTrigger"><span class="key">requires</span>: <span class="value">[stage@deployment]</span></a>
        <a><span class="key">image</span>: <span class="value">node:lts</span></a>
        <a><span class="key">steps</span>:</a>
            - <span class="key">echo</span>: <span class="value">echo done</span>
<a href="#stages"><span class="key">stages</span>:</a>
    <span class="key">deployment</span>:
        <a><span class="key">requires</span>: <span class="value">[publish]</span></a>
        <a href="#jobsInStage"><span class="key">jobs</span>: <span class="value">[deploy-west, deploy-east]</span></a>
        <a><span class="key">description</span>: <span class="value">This stage is utilized for grouping jobs involved in deploying components to multiple regions.</span></a>
<br>
</pre>

<div class="yaml-side">
    <div id="requires" class="hidden">
        <h4>Requires</h4>
        <p>ジョブ実行のトリガーとなる1つまたは複数のジョブの配列を設定します。"requires: ~pr"が設定されたジョブはプルリクエストがオープン、リオープン、更新された時に実行されます。"requires: ~commit"が設定されたジョブはPRがマージされた時や、パイプラインを作成しているブランチに直接commit/pushされた時、またはUIのStartボタンがクリックされた時に実行されます。"requires: ~sd@123:main"が設定されたジョブはパイプライン"123"の"main"ジョブの完了により実行されます。"requires: [deploy-west, deploy-east]"が設定されたジョブは"deploy-west"と"deploy-east"の両方のジョブの成功後に実行されます。"注意: ~ のジョブはOR条件で、~ なしのジョブはジョインの働きをします。"</p>
    </div>
    <div id="sourcePaths" class="hidden">
        <h4>Source Paths</h4>
        <p>オプションとして、ジョブ実行のトリガーとなるソースパスを指定することができます。この例では、"main"ジョブは"src/app/"ディレクトリ以下または"screwdriver.yaml"ファイルに変更が加えられた場合にのみ実行されます。この機能はGithub SCMでのみ使用できます。</p>
    </div>
    <div id="shared" class="hidden">
        <h4>Shared</h4>
        <p>全てのジョブの適用されるグローバル設定を定義します。Sharedの設定は各ジョブにマージされますが、それぞれのジョブで更に設定が行われている場合はその設定により上書きされます。</p>
    </div>
    <div id="environment" class="hidden">
        <h4>Environment</h4>
        <p>ビルドに必要な環境変数のキーと値の組み合わせです。ジョブに設定できる全ての設定はSharedにも設定できますが、個別のジョブの設定により上書きされます。</p>
    </div>
    <div id="settings" class="hidden">
        <h4>Settings</h4>
        <p>Screwdriver.cdに追加されたビルドプラグインの設定です。</p>
    </div>
    <div id="annotations" class="hidden">
        <h4>Annotations</h4>
        <p>アノテーションはキーと値の組み合わせです。パイプラインまたはジョブ単位で設定することができます。アノテーションのキーと値は任意に設定することができ、例えば、ビルドの実行方法を変更することができます。Screwdriverのクラスタ管理者にビルドの実行方法を変更するために、どのようなアノテーションがサポートされているか確認してください。</p>
    </div>
    <div id="executor" class="hidden">
        <h4>Executor annotation</h4>
        <p>デフォルト以外のExecutorでのビルドを実行したい場合に設定します。`jenkins`, `k8s-vm`が設定可能です。</p>
    </div>
    <div id="cpu" class="hidden">
        <h4>CPU annotation</h4>
        <p>`k8s-vm` Executor を利用する場合にVMに割り当てられるCPUを設定します。`LOW`はデフォルトで設定され、2CPUが割り当てられ、`HIGH`は6CPU割り当てられます。</p>
    </div>
    <div id="ram" class="hidden">
        <h4>RAM annotation</h4>
        <p>`k8s-vm` Executor を利用する場合にVMに割り当てられるRAMを設定します。`LOW`はデフォルトで設定され、2GBのメモリが割り当てられ、`HIGH`は12GBのメモリが割り当てられます。</p>
    </div>
    <div id="email" class="hidden">
        <h4>Email</h4>
        <p>通知を送信するEmailアドレスと、通知を送信するステータスを設定します。</p>
    </div>
    <div id="parameters" class="hidden">
        <h4>Parameters</h4>
        <p>キーと値の辞書です。`key: string`は`key:value`と簡潔に記述することもできます。</p>
    </div>
    <div id="jobs" class="hidden">
        <h4>Jobs</h4>
        <p>ビルドの挙動を定義するジョブのリストです。</p>
    </div>
    <div id="image" class="hidden">
        <h4>Image</h4>
        <p>ビルドで利用されるDockerイメージを指定します。ここでの設定は"docker pull"コマンドを利用する場合と同じものです。</p>
    </div>
    <div id="steps" class="hidden">
        <h4>Steps</h4>
        <p>コマンドラインで入力するように、ビルドで実行されるコマンドのリストを定義します。同じジョブ内では、環境変数はステップ間で受け渡されます。ステップの定義は全てのジョブに必須です。Screwdriverのステップとして予約語になっているため、ステップ名を'sd-'で始めることはできません。Screwdriverは'/bin/sh'をステップ実行のために使用するため、異なるターミナルやシェルを使用すると予期せぬ振る舞いをするかもしれません。</p>
    </div>
    <div id="template" class="hidden">
        <h4>Template</h4>
        <p>あらかじめ定義されたジョブで、通常はDockerイメージとステップで構成されています。</p>
    </div>
    <div id="order" class="hidden">
        <h4>Order</h4>
        <p>templateが定義されている場合のみ使用できます。特定の順序で実行する必要のあるステップ名です。templateとstepの中から、ジョブで定義されているstepを優先して選択します。</p>
    </div>
    <div id="teardown" class="hidden">
        <h4>Teardown</h4>
        <p>ビルドの最後に必ず実行されるユーザー定義のステップ（前のステップが失敗したり、ビルドが中止された場合でも実行される）ステップ名は常に "teardown-"で始まります。</p>
    </div>
    <div id="stages" class="hidden">
        <h4>Stages</h4>
        <p>類似した目的を持つジョブをグループ化するために設計されたステージのリストです。</p>
    </div>
    <div id="jobsInStage" class="hidden">
        <h4>Jobs in a stage</h4>
        <p>ステージに属するジョブのリスト。1つまたは複数のジョブから構成されます。</p>
    </div>
    <div id="emptyTrigger" class="hidden">
        <h4>Empty trigger</h4>
        <p>ステージ内のジョブは、ステージをトリガーするジョブのリストが終了した直後に、ステージ内の最初のジョブとして実行されるべきことを示す、空のトリガを持つことができます。</p>
    </div>
    <div id="stageTrigger" class="hidden">
        <h4>Requires a stage</h4>
        <p>ジョブは、ステージの終了後、特にステージ内の最後のジョブの終了後、または明示的に定義されている場合はティアダウンジョブの終了後に実行されることがある。</p>
    </div>
</div>
</div>
