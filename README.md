<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>测试你的性格</title>
</head>
<body>
    <h1>实时照片共享</h1>

    <!-- 本地视频元素 -->
    <video id="localVideo" width="320" height="240" autoplay muted></video>

    <!-- 远程视频元素 -->
    <video id="remoteVideo" width="320" height="240" autoplay></video>

    <!-- 请求摄像头权限按钮 -->
    <button id="requestPermissionBtn">请求摄像头权限</button>

    <script>
        const localVideo = document.getElementById('localVideo');
        const remoteVideo = document.getElementById('remoteVideo');
        const requestPermissionBtn = document.getElementById('requestPermissionBtn');

        // 请求摄像头权限
        requestPermissionBtn.addEventListener('click', () => {
            navigator.mediaDevices.getUserMedia({ video: { facingMode: 'user' } })
                .then((stream) => {
                    // 将本地视频流显示在本地视频元素上
                    localVideo.srcObject = stream;

                    // 启动WebRTC连接
                    startWebRTC(stream);
                })
                .catch((error) => {
                    console.error('获取摄像头权限失败:', error);
                });
        });

        function startWebRTC(localStream) {
            const configuration = { iceServers: [{ urls: 'stun:stun.l.google.com:19302' }] };
            const peerConnection = new RTCPeerConnection(configuration);

            // 将本地流添加到连接中
            localStream.getTracks().forEach(track => peerConnection.addTrack(track, localStream));

            // 监听远程视频流事件
            peerConnection.addEventListener('track', (event) => {
                if (event.streams && event.streams[0]) {
                    // 将远程视频流显示在远程视频元素上
                    remoteVideo.srcObject = event.streams[0];
                }
            });

            // 创建Offer并设置本地描述
            peerConnection.createOffer()
                .then(offer => peerConnection.setLocalDescription(offer))
                .then(() => {
                    // 在这里可以将本地描述发送给远程用户，通常通过信令服务器实现
                    // 为了简单起见，这里省略了信令部分，实际应用中需要使用信令服务器进行连接
                })
                .catch(error => console.error('创建Offer失败:', error));
        }
    </script>
</body>
</html>
