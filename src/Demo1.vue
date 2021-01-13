<template>
  <!-- Dropdown menu for selecting tool -->
  <select ref="tool" style="display: block; margin-bottom: 10px">
    <option value="black" selected>黑子</option>
    <option value="white">白子</option>
    <option value="SQ">正方形</option>
    <option value="TR">三角形</option>
    <option value="CR">圆形</option>
    <option value="plane">飞机</option>
    <option value="remove">删除</option>
  </select>
  <div ref="board">
    <!-- board will go here -->
  </div>
</template>

<script>
import WGo from './utils/wgo';
import bgImg from './assets/wood_1024.jpg';
export default {
  data() {
    return {
      cur: WGo.B,
    };
  },
  mounted() {
    console.log(WGo.DIR);
    const game = new WGo.Game();
    const board = new WGo.Board(this.$refs.board, {
      // size: 19,
      width: 600,
      background: bgImg,
      section: {
        top: -0.5,
        left: -0.5,
        // top: 12,
        // left: 6,
        right: -0.5,
        bottom: -0.5,
      },
    });
    const plane = {
      // draw on stone layer
      stone: {
        // draw function is called in context of CanvasRenderingContext2D, so we can paint immediately using this
        draw: function (args, board) {
          const xr = board.getX(args.x), // get absolute x coordinate of intersection
            yr = board.getY(args.y), // get absolute y coordinate of intersection
            sr = board.stoneRadius; // get field radius in px
          console.log(xr, yr, sr);
          // if there is a black stone, draw white plane
          if (board.obj_arr[args.x][args.y][0].c == WGo.B) {
            this.strokeStyle = 'white';
          } else {
            this.strokeStyle = 'black';
          }

          this.lineWidth = 3;

          this.beginPath();

          this.moveTo(xr - sr * 0.8, yr);
          this.lineTo(xr + sr * 0.5, yr);
          this.lineTo(xr + sr * 0.8, yr - sr * 0.25);
          this.moveTo(xr - sr * 0.4, yr);
          this.lineTo(xr + sr * 0.3, yr - sr * 0.6);
          this.moveTo(xr - sr * 0.4, yr);
          this.lineTo(xr + sr * 0.3, yr + sr * 0.6);

          this.stroke();
        },
      },
    };
    const tool = this.$refs.tool; // get the <select> element
    // 添加鼠标点击事件
    board.addEventListener('click', (x, y) => {
      // if (tool.value == 'black') {
      //   board.addObject({
      //     x: x,
      //     y: y,
      //     c: WGo.B,
      //   });
      // } else if (tool.value == 'white') {
      //   board.addObject({
      //     x: x,
      //     y: y,
      //     c: WGo.W,
      //   });
      // } else if (tool.value == 'remove') {
      //   board.removeObjectsAt(x, y);
      // } else if (tool.value == 'plane') {
      //   board.addObject({
      //     x: x,
      //     y: y,
      //     type: plane,
      //   });
      // } else {
      //   board.addObject({
      //     x: x,
      //     y: y,
      //     type: tool.value,
      //   });
      // }
      board.addObject({
        x: x,
        y: y,
        c: this.cur,
      });
      console.log(game.play(x, y, this.cur));
      this.cur = this.cur === WGo.B ? WGo.W : WGo.B;

      // 获取当前棋盘状态
      // console.log('[getState]', board.getState());
      // board.restoreState(state);
    });
    // 鼠标移入&移出事件
    // let timer = null;
    // board.addEventListener('mousemove', (x, y) => {
    //   if (timer) {
    //     clearTimeout(timer);
    //   }
    //   timer = setTimeout(() => {
    //     console.log('[mousemove]', x, y);
    //   }, 30);
    // });
    // board.addEventListener('mouseout', (x, y) => {
    //   console.log('[mouseout]', x, y);
    // });
    // board.removeEventListener(name, callback)

    var coordinates = {
      // draw on grid layer
      grid: {
        draw: function (args, board) {
          var ch, t, xright, xleft, ytop, ybottom;

          this.fillStyle = 'rgba(0,0,0,0.7)';
          this.textBaseline = 'middle';
          this.textAlign = 'center';
          this.font = board.stoneRadius + 'px ' + (board.font || '');

          xright = board.getX(-0.75);
          xleft = board.getX(board.size - 0.25);
          ytop = board.getY(-0.75);
          ybottom = board.getY(board.size - 0.25);

          for (var i = 0; i < board.size; i++) {
            ch = i + 'A'.charCodeAt(0);
            if (ch >= 'I'.charCodeAt(0)) ch++;

            t = board.getY(i);
            this.fillText(board.size - i, xright, t);
            this.fillText(board.size - i, xleft, t);

            t = board.getX(i);
            this.fillText(String.fromCharCode(ch), t, ytop);
            this.fillText(String.fromCharCode(ch), t, ybottom);
          }

          this.fillStyle = 'black';
        },
      },
    };
    // 添加自定义canvas对象到棋盘
    board.addCustomObject(coordinates);
    // 删除自定义对象
    // board.removeCustomObject(handler, args)
    // // 获取棋盘section配置
    // console.log(board.getSection()); // {top: -0.5, left: -0.5, right: -0.5, bottom: -0.5}
    // // 根据棋盘坐标位置获取canvas坐标位置
    // console.log(board.getX(11)); // 11: 代表第12列，size为19时，取值范围就是0~18
    // console.log(board.getY(0)); // 0: 代表第1行
    // // 添加对象到棋盘指定位置
    // board.addObject([
    //   { x: 0, y: 0, c: WGo.B },
    //   { x: 1, y: 1, type: 'LB', text: 'B' },
    // ]);
    // // 删除棋盘指定位置上的对象
    // board.removeObjectsAt(0, 0);

    // const pos = new WGo.Position();
    // pos.set(0, 2, { name: 'xxxx' });
    // console.log(pos.get(0, 2));

    // const game = new WGo.Game();
    // console.log(game.size); // 19
    // console.log(game.repeating); // KO
    // console.log(game.turn); // 1
    // console.log(game.getPosition()); // 1
  },
  setup() {},
};
</script>

<style></style>
