## Gymのインストール

基本的に、ここに書いてあるとおりにやります。
https://github.com/openai/gym

まずgymのソースコードをcloneします。システムを変更したくないので、pip install に--userオプションを付けます。

```
git clone https://github.com/openai/gym.git
cd gym
pip install -e . --user
```

Environmentがちゃんと動くかを少し確認してみましょう。

```
$ ipython
```

```
In [1]: import gym

In [2]: env=gym.make('Copy-v0')
[2016-12-06 14:56:33,403] Making new env: Copy-v0

In [3]: env.reset()
Out[3]: 4

In [4]: env.render()
Total length of input instance: 2, step: 0
==========================================
/home/tajima/Codes/gym/gym/envs/algorithmic/algorithmic_env.py:258: VisibleDeprecationWarning: converting an array with ndim > 0 to an index will result in an error in the future
  return self.input_data[pos]
Observation Tape    :   EC  
Output Tape         :   
Targets             :   EC  

```

AtariのEnvironmentを入れてみましょう。
```
pip install gym[atari]
```

```
In [2]: import gym

In [3]: env=gym.make('SpaceInvaders-v0')
[2016-12-06 14:59:11,076] Making new env: SpaceInvaders-v0

In [4]: env.reset()
Out[4]: 
array([[[ 0,  0,  0],
        [ 0,  0,  0],
        [ 0,  0,  0],
        ..., 
        [ 0,  0,  0],
        [ 0,  0,  0],
        [ 0,  0,  0]],

       [[ 0,  0,  0],
        [ 0,  0,  0],
        [ 0,  0,  0],
        ..., 
        [ 0,  0,  0],
        [ 0,  0,  0],
        [ 0,  0,  0]],

       [[ 0,  0,  0],
        [ 0,  0,  0],
        [ 0,  0,  0],
        ..., 
        [ 0,  0,  0],
        [ 0,  0,  0],
        [ 0,  0,  0]],

       ..., 
       [[80, 89, 22],
        [80, 89, 22],
        [80, 89, 22],
        ..., 
        [80, 89, 22],
        [80, 89, 22],
        [80, 89, 22]],

       [[80, 89, 22],
        [80, 89, 22],
        [80, 89, 22],
        ..., 
        [80, 89, 22],
        [80, 89, 22],
        [80, 89, 22]],

       [[80, 89, 22],
        [80, 89, 22],
        [80, 89, 22],
        ..., 
        [80, 89, 22],
        [80, 89, 22],
        [80, 89, 22]]], dtype=uint8)

In [5]: env.render()
```

インベーダゲームの画面が表示されました。

いろいろな環境は、

```
$ pip install gym[環境名]
```

でインストールできます。すべてをインストールする場合は、

```
$ pip install gym[all]
```

ですべてがインストールされます。ただし、依存しているパッケージ(swigな
ど)があり、別途入れておく必要があります。

適当な環境で遊んでみましょう。

```
env=gym.make('SpaceInvaders-v0')
env.reset()
```
```
for _ in range(1000):           
    env.render()
    env.step(env.action_space.sample())

```

ランダムな動きでスペースインベーダがプレイされました。

###

stepファンクションが返すものは、

- Observation(object):
 環境依存の観測データ。画像とか、関節角度、関節速度、ボードゲームの状態など。

- reward(float)

前回の行動により得られる報酬。報酬の合計(価値)を増やすのが強化学習の目的です。

- done(boolean)

環境をresetすべきかを示す変数。たとえば、ポールが倒れた、すべてのライフを失った、など。いわゆるエピソードの終わりを示す。

- info(dict)

主にデバッグ用のデータが得られる。これを学習に使う場合もあるが、公式に
はつかってはいけない。

##環境はどんな観測と行動を返すか？

スペースインベーダの場合、

```
In [19]: env.action_space
Out[19]: Discrete(6)

In [20]: env.observation_space
Out[20]: Box(210, 160, 3)
```

OpenAIからUniverseというのが公開されました。

https://github.com/openai/universe

すべてのコンピュータゲーム
を上手にプレイできるようなAgentを訓練しようというのが目的です。Gymに任
意のゲーム画面をつなげるためのプラットフォームのようです。

gymの環境を自動的にDockerで立ち上げる、みたいなことができます。


```
import gym
import universe  # register the universe environments

env = gym.make('flashgames.DuskDrive-v0')
env.configure(remotes=1)  # automatically creates a local docker container
observation_n = env.reset()

while True:
  action_n = [[('KeyEvent', 'ArrowUp', True)] for ob in observation_n]  # your agent here
  observation_n, reward_n, done_n, info = env.step(action_n)
  env.render()
```

とっかかりとして、universe-starter-agentが公開されている。

https://github.com/openai/universe-starter-agent


Universeのインストールがうまく行かない

https://github.com/openai/universe/issues/1

インストール時のロケールの問題らしく、
```
$ export LANG=C
```
として
```
pip install -e .
```
としたら解決。

dockerpycredsがないと言われる。
```
pip install -U docker-py 
```

txaioが無いと言われる。
```
pip install txaio
```

プロキシの中だったので、結局サンプルも動かず…
