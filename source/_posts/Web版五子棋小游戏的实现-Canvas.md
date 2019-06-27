---
title: Web版五子棋小游戏的实现-Canvas
date: 2017-06-18 19:38:40
tags: 五子棋,canvas
---


##  需求描述

* 用canvas和dom两种方案写一个五子棋小游戏，不需要考虑人机对战。
* 库和框架等不做要求，随意选择。

想了半天，决定先实现canvas版。 除了以上两点需求之外，还应该完成如下要求：

* 额外应该还要考虑到拓展性，实现棋盘列数、颜色、棋子半径等灵活配置
* 在dom和canvas切换时实现的代价最小。

##  DEMO及代码
具体代码可到我的github下载： [**canvas版五子棋**](https://github.com/tuzilingdang/backgammon-canvas)

DEMO地址：[**DEMO**](https://tuzilingdang.github.io/backgammon-canvas/)

![canvas实现的五子棋](/img/Web版五子棋小游戏的实现-Canvas/1.png)

##  思路

1. 使用require.js实现模块化，整体代码比较简单，使用原生js或jquery即可
2. 棋盘和棋子的绘制使用canvas实现
3. 组合使用构造函数和原型模式，构造棋盘，棋子和Game的原型

##  具体实现和分析

###  目录结构

![目录结构](/img/Web版五子棋小游戏的实现-Canvas/2.png)

###  入口函数
入口函数在app.js中， 因依赖于require实现代码的模块化，需要对require做一个config配置：

```
require.config({
    baseUrl: 'js/libs',
    paths: {
        app: '../app',
        game: "../app/game",
        checkerboard: "../app/checkerboard",
        piece: "../app/piece",
    }
});

```

app主逻辑入口如下：

首先做好棋盘各属性的配置，还有棋子的半径定义。然后初始化Game。

```
require(["jquery", "game"], function($, Game) {
    var checkerBoard = {
        id: "checker-board",
        rows: 20, // 棋盘列数
        margin: 20, // 棋盘边框间距

    };
    var piece = {
        r : 24
    };

    var game = new Game( checkerBoard, piece );
    game.init();

    $("#start").click(function() {
        game.init();
        game.start();
        $("#start").unbind();
    });
});
```


###  五子棋的主要算法

主要算法想过两种方案： 

#### 方案1
这个方案比较麻烦，如果是三子连胜比较简单的话可以采用。大体思路是： 

1. 设置一个5*5的正方形作为检测窗口，窗口从左上角开始，每次以step1格的距离像右移动，移动到最右边，然后以step为1格想下移动。若棋盘矩阵的长宽为n，也就是检测窗口一共移动了n-1 * n-1次。
2. 每次检测窗口移动，都要匹配对应棋盘的棋子状态是否与检测窗口的胜利状态一致。 检测窗口中胜利状态一共有12种，分别如下
3.   棋盘对应窗口的棋子与以上状态一致，则游戏结束。



| X | X | X | X | X |			
|:--|:--|:--|:--|:--|
| O | O | O | O | O |
| O | O | O | O | O |
| O | O | O | O | O |
| O | O | O | O | O |

……


| X | O | O | O | O |			
|:--|:--|:--|:--|:--|
| X | O | O | O | O |
| X | O | O | O | O |
| X | O | O | O | O |
| X | O | O | O | O |

……

| X | O | O | O | O |
|:--|:--|:--|:--|:--|
| O | X | O | O | O |
| O | O | X | O | O |
| O | O | O | X | O |
| O | O | O | O | X |





#### 方案2

目前看起来最快的实现方法：

1. 每次下一个子，检察一下棋子周边的状态
2. 分别检测横向、竖向、斜向、反斜向的是否有连子，如果有，则判断颜色，返回胜方及结束状态。
3. 这里比较容易出错的是边界检测，如果边界检测代码写的有问题，会出现各种在边界点击的报错提示。
4. 添加了和棋的判断，毕竟有时格子太少，容易出现和棋的状态。

代码如下：

``` c
		caculateWinner: function(pos) {
			var that = this;
			var player = that.stateMatrix[pos.x][pos.y];

			function checkDrawn() {
				for (var i = 0; i < that.matrixWidth; i++) {
					for (var j = 0; j < that.matrixHeight; j++) {
						if (that.stateMatrix[i][j] === "null") {
							return false;
						}
					}
				}
				return true;
			}

			function checkHorizon() {
				var count = 0;
				for (var i = 1; i < 5; i++) {
					if ((pos.x - i) >= 0) {
						if ((that.stateMatrix[pos.x - i][pos.y] === player)) {
							count++;
						}
					}
				}

				for (var i = pos.x + 1; i < 5 - count + pos.x; i++) {
					if (i >= that.matrixWidth ) {
						return false;
					} else {
						if (that.stateMatrix[i][pos.y] !== player) {
							return false;
						}
					}
				}
				return true;
			}

			function checkVertical() {
				var count = 0;
				for (var j = 1; j < 5; j++) {
					if ((pos.y - j) >= 0) {
						if ((that.stateMatrix[pos.x][pos.y - j] === player)) {
							count++;
						}
					}
				}

				for (var j = pos.y + 1; j < 5 - count + pos.y; j++) {
					if (j >= that.matrixHeight ) {
						return false;
					} else {
						if (that.stateMatrix[pos.x][j] !== player) {
							return false;
						}
					}

				}
				return true;
			}

			function checkDiagonal() {
				var count = 0;
				for (var j = 1; j < 5; j++) {
					if ((pos.y - j) >= 0 && (pos.x - j) >= 0) {
						if ((that.stateMatrix[pos.x - j][pos.y - j] === player)) {
							count++;
						}
					}
				}
				for (var j = 1; j < 5 - count; j++) {
					if ((j + pos.y >= that.matrixHeight ) || (j + pos.x >= that.matrixHeight )) {
						return false;
					} else {
						if (that.stateMatrix[pos.x + j][pos.y + j] !== player) {
							return false;
						}
					}
				}
				return true;
			}

			function checkReverseDiagonal() {
				var count = 0;
				for (var j = 1; j < 5; j++) {
					if ((pos.y - j) >= 0 && (j + pos.x) < that.matrixWidth ) {
						if ((that.stateMatrix[pos.x + j][pos.y - j] === player)) {
							count++;
						}
					}
				}
				for (var j = 1; j < 5 - count; j++) {
					if ( ((j + pos.y) >= that.matrixHeight ) || ((pos.x - j) < 0) ){
						return false;
					} else {
						if (that.stateMatrix[pos.x - j][pos.y + j] !== player) {
							return false;
						}
					}
				}
				return true;
			}

			if (!checkDrawn()) {
				if (checkHorizon() || checkVertical() || checkDiagonal() || checkReverseDiagonal()) {
					return player;
				} else {
					return "N";
				}
			} else {
				return "D";
			}
		}

```




