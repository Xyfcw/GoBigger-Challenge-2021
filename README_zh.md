# GoBigger Challenge 2021

[en](https://github.com/opendilab/GoBigger-Challenge-2021/blob/main/README.md) / [中文](https://github.com/opendilab/GoBigger-Challenge-2021/blob/main/README_zh.md)

* 2021.11.13 我们正在举办一场[竞赛](https://www.datafountain.cn/competitions/549/) —— 2021“AI球球大作战：Go-Bigger多智能体决策智能挑战赛”. 快来参与吧！

## 赛制说明

多智能体对抗是决策AI中重要的一部分，也是一个非常具有挑战性的难题。为了丰富多智能体对抗环境，OpenDILab研发了一款多智能体对抗竞技游戏，即GoBigger。本次大赛旨在探索多智能体博弈的研究，推动决策智能相关领域的技术人才培养，从而打造“全球领先”、“原创”、“开放”的决策AI开源技术生态。

本次大赛需要参赛选手提交他们的智能体以参加评测。我们会返回智能体的评测分数，以帮助参赛选手对提交的智能体的性能能有更正确的认识。在比赛的最后阶段，我们会对提交的智能体进行公平且完全的测试，并对各参赛队伍进行最终排名。

### 具体任务

本次竞赛采用 [Go-Bigger](https://github.com/opendilab/GoBigger) 作为游戏环境。Go-Bigger 是一款多人组队竞技游戏。更多细节请参考 Go-Bigger 文档。在游戏中，每支竞赛参赛队伍控制游戏中一支队伍（每支队伍由多个玩家组成）。竞赛参赛队伍需要通过提交智能体的方式，来对游戏中的某个队伍及其所包含的玩家进行控制，通过团队配合来获取更高的分数，从而在游戏中取得更高的排名。

### 提交方式

竞赛为所有参赛者提供了示例代码，具体可以查看 [submit](https://github.com/opendilab/GoBigger-Challenge-2021/blob/main/submit)。同样，竞赛也提供了一些基础代码，参赛者可以在此基础上实现对应的方法，从而得到对应的提交。

竞赛提供的 [BaseSubmission](https://github.com/opendilab/GoBigger-Challenge-2021/blob/main/submit/base_submission.py) 内容如下：

```python
class BaseSubmission:

    def __init__(self, team_name, player_names):
        self.team_name = team_name
        self.player_names = player_names

    def get_actions(self, obs):
        '''
        Overview:
            You must implement this function.
        '''
        raise NotImplementedError
```

注意，所有的提交都应当继承 `BaseSubmission` 类，提交的类名称应当为 `MySubmission`。竞赛将会为每个提交提供 `team_name` 和 `player_names` 作为类的输入参数。`team_name` 代表的是该提交所控制的队伍的名称，在每一场比赛都会不一样。`player_names` 代表当前队伍中的所有控制的玩家的名称。竞赛在评测用户提交的时候会调用 `get_actions()` 以获取该提交在当前帧的 `action`。`get_actions()` 方法以 `obs` 作为输入（与 [tutorial](https://gobigger.readthedocs.io/zh_CN/latest/tutorial/space.html#observation-space) 相似）。例如，一个简单的 `obs` 例子如下：

```python
global_state, player_state = obs
```

`gloabl_state` 包含地图尺寸，总时间，已持续时间，以及排行榜等信息。具体如下：

```python
{
    'border': [map_width, map_height], # the map size
    'total_time': match_time, # the duration of a game
    'last_time': last_time,   # the length of time a game has been played
    'leaderboard': {
        team_name: team_size
    } # the team with its size in this game
}
```

``player_state`` 包含玩家channel信息，玩家视角所在位置，玩家视角内所有出现的单位信息，当前玩家所属队伍名称。具体如下:

```
{
    player_name: {
        'feature_layers': list(numpy.ndarray), # features of player
        'rectangle': [left_top_x, left_top_y, right_bottom_x, right_bottom_y], # the vision's position in the map
        'overlap': {
            'food': [[position.x, position.y, radius], ...], 
            'thorns': [[position.x, position.y, radius], ...],
            'spore': [[position.x, position.y, radius], ...],
            'clone': [[[position.x, position.y, radius, player_name, team_name], ...],     
        }, # all balls' info in vision
        'team_name': team_name, # the team which this player belongs to 
    }
}
```

需要注意的是，竞赛只会为当前提交提供属于他的玩家的信息。例如，当前提交所控制的队伍名下有两个玩家，分别为玩家A和玩家B，同时还有玩家C属于另一队伍，那么该提交能获取到的信息只包含玩家A和玩家B的信息，不会包含玩家C的信息。

在获取到 `obs` 之后，提交应当返回 `actions`。一个简单的 `actions` 样例如下：

```python
{
    player_a: actions_a,
    player_b: actions_b
}
```

`player_a` 和 `player_b` 分别是该提交所控制的玩家名称，即 `player_names` 中的名称。`actions_a` 和 `actions_b` 应当是列表类型，其内容应当与 [action-space](https://gobigger.readthedocs.io/zh_CN/latest/tutorial/space.html#id2) 中保持一致。

### 提交样例与测试

竞赛提供了 [RandomSubmission](https://github.com/opendilab/GoBigger-Challenge-2021/blob/main/submit/random_submission.py) 和 [BotSubmission](https://github.com/opendilab/GoBigger-Challenge-2021/blob/main/submit/bot_submission.py) 以供参考。`RandomSubmission` 返回随机动作，而 `BotSubmission` 会按照一定的规则返回动作。

此外，竞赛还提供了一个简单的提交流程样例。具体细节请查看 [submission_example](https://github.com/opendilab/GoBigger-Challenge-2021/blob/main/submit/submission_example/)。用户可以在这个目录下开发自己的提交。开发完成之后，可以通过目录下的测试文件进行测试（`python -u test.py`），以检测提交是否满足要求，并生成对应的 `.tar.gz` 文件用于提交。

### 附加材料

如果用户想要在提交中使用一些其他材料，例如模型检查点或任何其他文件，请将他们统一放置在 `./supplements` 下。

### 最终提交

最终提交的 `.tar.gz` 文件解压后的目录格式如下：

```
- my_submission
    | - __init__.py
    | - requirements.txt
    | - my_submission.py
    | - supplements/
        | - checkpoints or other materials
```

注意，`__init__.py` 应当是一个空文件。

## 尝试你的第一个提交

也许你对我们的比赛还不是很熟悉，但是不用担心，我们提供了一个最简单的提交案例！尝试下面的代码快速生成一个用于提交的 `my_submission.tar.gz` 吧！

```
$ cd submit/submission_example
$ python -u test.py
```
上述的 `test.py` 会检查你的提交是否正确。如果正确，则会得到以下输出：

```
Success!
###################################################################
#                                                                 #
#   Now you can upload my_submission.tar.gz as your submission.   #
#                                                                 #
###################################################################
```
现在你只需要把 `my_submission.tar.gz` 提交即可！

* 注意：此提交是由随机策略构成的。你可以检查代码并更改策略以获得更好的性能。

## 基于DI-engine实现提交
我们也基于 DI-engine 实现了一个提交，具体请查看 [submission_example_di](https://github.com/opendilab/GoBigger-Challenge-2021/blob/main/di_baseline/)。用户可以将训练好的ckpt放入supplements下，即可组成一个完整的提交。

## 其他

* 赛题内容设置: [en](https://github.com/opendilab/GoBigger-Challenge-2021/blob/main/challenge_settings.md) / [中文](https://github.com/opendilab/GoBigger-Challenge-2021/blob/main/challenge_settings_zh.md)
* 评测方法: [en](https://github.com/opendilab/GoBigger-Challenge-2021/blob/main/evaluation.md) / [中文](https://github.com/opendilab/GoBigger-Challenge-2021/blob/main/evaluation_zh.md)
