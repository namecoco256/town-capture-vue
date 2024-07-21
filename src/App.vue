<script setup>
  import { onMounted, ref, watch } from 'vue'
  /// 変数の定義 ///

  //要素を参照するやつ
  let video
  let canvas

  const screenSize = { width: 992, height: 744 };
  const canvasSize = { width: 992, height: 744 }

  //画面キャプチャAPIのアレ
  let media
  let mediaSize = screenSize//screenSizeは仮置き
  let canvasCtx
  let canvasId
  /// 変数の定義 ここまで ///



  /// 起動時のセットアップ ///
  onMounted(() => {
    video = document.querySelector('video')
    canvas = document.querySelector('canvas')
    canvasCtx = canvas.getContext('2d')

    onWindowSelect()
  })
  /// 起動時のセットアップ ここまで///



  ///// 画面取得とレンダリング /////
  function onWindowSelect() {
    // video要素にキャプチャ画面を表示する
    console.log('start')
    media = navigator.mediaDevices.getDisplayMedia({
      audio: false,
      video: true
    }).then(function (stream) {
      video.srcObject = stream

      //mediaの縦と横のピクセル数を代入。このgetVideoTracks()[0].getSettings().widthってやつ探すの死ぬほど大変だったんだけど！！！！
      mediaSize.width = video.srcObject.getVideoTracks()[0].getSettings().width
      mediaSize.height = video.srcObject.getVideoTracks()[0].getSettings().height
      console.log(mediaSize)
      //キャプチャ画面を表示
      isWindowSelected.value = true
    });
    
    _canvasUpdate()
  }

  function _canvasUpdate() {
    // videoRendering()
    canvasId = requestAnimationFrame(_canvasUpdate)
  };

  //キャプチャ画面をレンダリングする関数
  function videoRendering(){
    //まず画面全体を白でリセット
    canvasCtx.fillStyle = "white";
    canvasCtx.fillRect(0, 0, canvas.width, canvas.height)

    //縦横比を合わせつつキャンバスの大きさに合うよう拡大縮小
    if (canvas.width / mediaSize.width * mediaSize.height <= canvas.height) {//縦幅がcanvasの範囲に収まったら
      //横幅を固定して縦幅を調節する
      let fixedHeight = canvas.width / mediaSize.width * mediaSize.height
      //画面中央に表示
      canvasCtx.drawImage(video, 0, (canvas.height - fixedHeight) / 2, canvas.width, fixedHeight)
    } else {
      let fixedWidth = canvas.height / mediaSize.height * mediaSize.width
      canvasCtx.drawImage(video, (canvas.width - fixedWidth) / 2, 0, fixedWidth, canvas.height)
    }
  }


</script>

<template>
  <video id="video" autoplay style="display:none;"></video>
    
  <canvas id="canvas" :width="canvasSize.width" :height="canvasSize.height"></canvas>
  <button @click="videoRendering">Capture</button>
</template>

<style scoped>

</style>
