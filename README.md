// 生成随机验证码
function generateCaptcha() {
    const chars = '0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ';
    let captcha = '';
    for (let i = 0; i < 4; i++) {
        captcha += chars[Math.floor(Math.random() * chars.length)];
    }
    return captcha;
}

// 在画布上绘制验证码
function drawCaptcha(captcha) {
    const canvas = document.getElementById('captchaCanvas');
    if (!canvas) {
        console.error('找不到验证码画布元素');
        return;
    }
    
    const ctx = canvas.getContext('2d');
    
    // 设置画布大小
    canvas.width = 100;
    canvas.height = 40;

    // 清除之前的内容
    ctx.clearRect(0, 0, canvas.width, canvas.height);

    // 填充白色背景
    ctx.fillStyle = '#fff';
    ctx.fillRect(0, 0, canvas.width, canvas.height);

    // 绘制干扰线
    for (let i = 0; i < 3; i++) {
        ctx.beginPath();
        ctx.moveTo(Math.random() * canvas.width, Math.random() * canvas.height);
        ctx.lineTo(Math.random() * canvas.width, Math.random() * canvas.height);
        ctx.strokeStyle = `rgb(${Math.random() * 255},${Math.random() * 255},${Math.random() * 255})`;
        ctx.stroke();
    }

    // 绘制验证码文字
    ctx.font = 'bold 24px Arial';
    ctx.textAlign = 'center';
    ctx.textBaseline = 'middle';
    
    for (let i = 0; i < captcha.length; i++) {
        const x = 20 + i * 20;
        const y = canvas.height / 2;
        ctx.fillStyle = `rgb(${Math.random() * 150},${Math.random() * 150},${Math.random() * 150})`;
        ctx.fillText(captcha[i], x, y);
    }
}

// 当前验证码
let currentCaptcha = '';

// 刷新验证码
function refreshCaptcha() {
    currentCaptcha = generateCaptcha();
    console.log('生成新验证码:', currentCaptcha);
    drawCaptcha(currentCaptcha);
}

// 处理登录
function handleLogin(event) {
    event.preventDefault();
    
    const idNumber = document.getElementById('idNumber').value;
    const password = document.getElementById('password').value;
    const captcha = document.getElementById('captcha').value;
    const errorMessage = document.getElementById('errorMessage');
    const loginBtn = document.querySelector('.login-btn');

    // 验证身份证号/准考证号
    if (!/^\d{15,18}$/.test(idNumber)) {
        errorMessage.textContent = '请输入有效的证件号码';
        return false;
    }

    // 验证密码
    if (!/^(?=.*[A-Za-z])(?=.*\d)[A-Za-z\d@]{8,}$/.test(password)) {
        errorMessage.textContent = '密码必须包含至少8位数字和字母';
        return false;
    }

    // 验证验证码
    if (captcha.toLowerCase() !== currentCaptcha.toLowerCase()) {
        errorMessage.textContent = '验证码错误';
        refreshCaptcha();
        document.getElementById('captcha').value = '';
        return false;
    }

    // 显示加载状态
    loginBtn.disabled = true;
    loginBtn.innerHTML = '<i class="fas fa-spinner fa-spin"></i> 正在登录...';
    errorMessage.textContent = '';

    // 模拟登录请求延迟
    setTimeout(() => {
        // 验证用户名和密码
        if (idNumber === '652922200306260531' && password === 'Qd852456@') {
            showScoreModal({
                name: '焦**',
                idNumber: '652922********0531',
                school: '陕西科技大学',
                reportId: '242261043001216',
                scores: {
                    total: 433,
                    listening: 142,
                    reading: 159,
                    writing: 132,
                    speaking: 'B',
                    examId: '610431242211907'
                }
            });
        } else {
            errorMessage.textContent = '证件号码或密码错误，请重新输入';
            refreshCaptcha();
            document.getElementById('captcha').value = '';
        }

        // 恢复按钮状态
        loginBtn.disabled = false;
        loginBtn.innerHTML = '登录';
    }, 2000);

    return false;
}

// 显示成绩查询模态框
function showScoreModal(data) {
    const modal = document.createElement('div');
    modal.className = 'score-modal';
    modal.innerHTML = `
        <div class="score-content">
            <h2>全国大学英语六级考试(CET6)成绩详情</h2>
            
            <div class="personal-info">
                <div class="info-wrapper">
                    <div class="info-main">
                        <div class="info-item">
                            <span class="label">姓名：</span>
                            <span class="value">${data.name}</span>
                        </div>
                        <div class="info-item">
                            <span class="label">证件号码：</span>
                            <span class="value">${data.idNumber}</span>
                        </div>
                        <div class="info-item">
                            <span class="label">学校名称：</span>
                            <span class="value">${data.school}</span>
                        </div>
                        <div class="info-item">
                            <span class="label">成绩报告单编号：</span>
                            <span class="value">${data.reportId}</span>
                        </div>
                    </div>
                </div>
            </div>

            <div class="score-tables">
                <div class="score-section">
                    <h3>笔试成绩</h3>
                    <table>
                        <tr>
                            <th>准考证号</th>
                            <td colspan="4">${data.scores.examId}</td>
                        </tr>
                        <tr>
                            <th>总分</th>
                            <th>听力</th>
                            <th>阅读</th>
                            <th>写作与翻译</th>
                        </tr>
                        <tr>
                            <td style="color: #2ecc71; font-weight: bold;">${data.scores.total}</td>
                            <td>${data.scores.listening}</td>
                            <td>${data.scores.reading}</td>
                            <td>${data.scores.writing}</td>
                        </tr>
                    </table>
                </div>

                <div class="score-section">
                    <h3>口试成绩</h3>
                    <table>
                        <tr>
                            <th>准考证号</th>
                            <th>成绩</th>
                        </tr>
                        <tr>
                            <td>--</td>
                            <td>--</td>
                        </tr>
                    </table>
                </div>
            </div>

            <div class="notice-text">
                您在报名期间已选择需要纸质成绩报告单，2月26日6时至2月28日17时可再次登录报名网站（<a href="http://cet-bm.neea.edu.cn" target="_blank">cet-bm.neea.edu.cn</a>）进行修改。
            </div>

            <div class="modal-buttons">
                <button class="close-btn" onclick="closeScoreModal()">
                    <i class="fas fa-arrow-left"></i> 返回
                </button>
            </div>
        </div>
    `;
    document.body.appendChild(modal);
    
    setTimeout(() => {
        modal.classList.add('show');
    }, 10);
}

// 关闭成绩查询模态框
function closeScoreModal() {
    const modal = document.querySelector('.score-modal');
    modal.classList.remove('show');
    setTimeout(() => {
        modal.remove();
    }, 300);
}

// 页面加载完成后初始化
document.addEventListener('DOMContentLoaded', () => {
    console.log('页面加载完成，初始化验证码');
    refreshCaptcha();
    
    // 添加点击刷新按钮的事件监听
    const refreshBtn = document.querySelector('.refresh-btn');
    if (refreshBtn) {
        refreshBtn.addEventListener('click', () => {
            console.log('点击刷新验证码');
            refreshCaptcha();
        });
    }
}); 
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    font-family: "Microsoft YaHei", Arial, sans-serif;
    background-color: #f5f5f5;
    color: #333;
    line-height: 1.6;
}

header {
    background-color: #fff;
    padding: 1rem;
    text-align: center;
    box-shadow: 0 2px 4px rgba(0,0,0,0.1);
}

header h1 {
    color: #2c3e50;
    font-size: 1.8rem;
}

main {
    min-height: calc(100vh - 200px);
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 2rem;
}

.login-container {
    background-color: #fff;
    padding: 2rem;
    border-radius: 8px;
    box-shadow: 0 4px 6px rgba(0,0,0,0.1);
    width: 100%;
    max-width: 400px;
}

h2 {
    text-align: center;
    color: #2c3e50;
    margin-bottom: 2rem;
}

.input-group {
    margin-bottom: 1.5rem;
    position: relative;
}

.input-group i {
    position: absolute;
    left: 12px;
    top: 12px;
    color: #7f8c8d;
}

.input-group input {
    width: 100%;
    padding: 10px 10px 10px 35px;
    border: 1px solid #ddd;
    border-radius: 4px;
    font-size: 1rem;
    transition: border-color 0.3s;
}

.input-group input:focus {
    outline: none;
    border-color: #3498db;
}

.input-group small {
    display: block;
    color: #7f8c8d;
    font-size: 0.8rem;
    margin-top: 0.5rem;
}

.captcha-group {
    display: flex;
    flex-direction: column;
}

.captcha-container {
    display: flex;
    align-items: center;
    gap: 1rem;
    margin-top: 0.5rem;
}

#captchaCanvas {
    border: 1px solid #ddd;
    border-radius: 4px;
    height: 40px;
    width: 100px;
    background-color: #fff;
}

.refresh-btn {
    background: none;
    border: none;
    color: #3498db;
    cursor: pointer;
    font-size: 0.9rem;
    white-space: nowrap;
    padding: 5px 10px;
}

.refresh-btn:hover {
    text-decoration: underline;
}

.error-message {
    color: #e74c3c;
    text-align: center;
    margin-bottom: 1rem;
    min-height: 1.5rem;
}

.login-btn {
    width: 100%;
    padding: 12px;
    background-color: #3498db;
    color: #fff;
    border: none;
    border-radius: 4px;
    font-size: 1.1rem;
    cursor: pointer;
    transition: background-color 0.3s;
}

.login-btn:hover {
    background-color: #2980b9;
}

.links {
    display: flex;
    justify-content: space-between;
    margin-top: 1rem;
}

.links a {
    color: #3498db;
    text-decoration: none;
    font-size: 0.9rem;
}

.links a:hover {
    text-decoration: underline;
}

footer {
    text-align: center;
    padding: 1rem;
    background-color: #fff;
    color: #7f8c8d;
    font-size: 0.9rem;
}

footer p {
    margin: 0.5rem 0;
}

@media (max-width: 480px) {
    .login-container {
        padding: 1.5rem;
    }

    header h1 {
        font-size: 1.5rem;
    }

    .captcha-container {
        flex-wrap: wrap;
    }
    
    #captchaCanvas {
        width: 120px;
        height: 45px;
    }
    
    .refresh-btn {
        padding: 8px 12px;
    }
}

/* 成绩查询模态框样式 */
.score-modal {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: rgba(0, 0, 0, 0.5);
    display: flex;
    justify-content: center;
    align-items: center;
    opacity: 0;
    transition: opacity 0.3s ease;
    z-index: 1000;
}

.score-modal.show {
    opacity: 1;
}

.score-content {
    background-color: #fff;
    padding: 2rem;
    border-radius: 8px;
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
    width: 90%;
    max-width: 800px;
    max-height: 90vh;
    overflow-y: auto;
}

.score-content h2 {
    color: #2c3e50;
    text-align: center;
    margin-bottom: 2rem;
    font-size: 1.8rem;
}

.personal-info {
    background-color: #f8f9fa;
    padding: 1.5rem;
    border-radius: 8px;
    margin-bottom: 2rem;
}

.info-item {
    margin-bottom: 1rem;
    display: flex;
    gap: 1rem;
}

.info-item:last-child {
    margin-bottom: 0;
}

.info-item .label {
    color: #7f8c8d;
    min-width: 100px;
}

.info-item .value {
    color: #2c3e50;
    font-weight: 500;
}

.score-tables {
    display: grid;
    gap: 2rem;
    margin-bottom: 2rem;
}

.score-section h3 {
    color: #2c3e50;
    margin-bottom: 1rem;
    font-size: 1.2rem;
}

.score-section table {
    width: 100%;
    border-collapse: collapse;
    margin-bottom: 1rem;
}

.score-section th,
.score-section td {
    padding: 1rem;
    text-align: center;
    border: 1px solid #ddd;
}

.score-section th {
    background-color: #f8f9fa;
    color: #2c3e50;
    font-weight: 500;
}

.score-section td {
    color: #2c3e50;
    font-size: 1.1rem;
}

.score-section table th {
    background-color: #f8f9fa;
    color: #2c3e50;
    font-weight: 500;
    min-width: 120px;
}

.score-section table td {
    color: #2c3e50;
    font-size: 1.1rem;
    padding: 1rem 2rem;
}

.modal-buttons {
    display: flex;
    justify-content: center;
    gap: 1rem;
}

.modal-buttons button {
    padding: 0.8rem 1.5rem;
    border: none;
    border-radius: 4px;
    cursor: pointer;
    font-size: 1rem;
    display: flex;
    align-items: center;
    gap: 0.5rem;
    transition: background-color 0.3s;
}

.print-btn {
    background-color: #3498db;
    color: #fff;
}

.print-btn:hover {
    background-color: #2980b9;
}

.close-btn {
    background-color: #95a5a6;
    color: #fff;
}

.close-btn:hover {
    background-color: #7f8c8d;
}

/* 打印样式 */
@media print {
    body > *:not(.score-modal) {
        display: none;
    }

    .score-modal {
        position: static;
        background: none;
    }

    .score-content {
        box-shadow: none;
        max-width: 100%;
    }

    .modal-buttons {
        display: none;
    }
}

/* 响应式设计 */
@media (max-width: 768px) {
    .score-content {
        padding: 1rem;
        width: 95%;
        margin: 1rem;
    }

    .score-content h2 {
        font-size: 1.2rem;
        margin-bottom: 1rem;
    }

    /* 个人信息响应式 */
    .personal-info {
        padding: 1rem;
    }

    .info-item {
        flex-direction: column;
        gap: 0.3rem;
        margin-bottom: 0.8rem;
    }

    .info-item .label {
        min-width: auto;
        color: #2c3e50;
        font-weight: 500;
    }

    /* 成绩表格响应式 */
    .score-tables {
        gap: 1rem;
    }

    .score-section h3 {
        font-size: 1.1rem;
        margin-bottom: 0.5rem;
    }

    /* 让表格可以水平滚动 */
    .score-section {
        overflow-x: auto;
        -webkit-overflow-scrolling: touch;
    }

    .score-section table {
        min-width: 100%;
        white-space: nowrap;
    }

    .score-section th,
    .score-section td {
        padding: 0.5rem;
        font-size: 0.9rem;
    }

    /* 调整按钮大小 */
    .modal-buttons {
        flex-direction: column;
        gap: 0.5rem;
    }

    .modal-buttons button {
        width: 100%;
        padding: 0.6rem 1rem;
        justify-content: center;
    }
}

/* 超小屏幕设备 */
@media (max-width: 320px) {
    .score-content {
        padding: 0.8rem;
    }

    .score-content h2 {
        font-size: 1rem;
    }

    .score-section th,
    .score-section td {
        padding: 0.4rem;
        font-size: 0.8rem;
    }
}

/* 打印样式优化 */
@media print {
    .score-content {
        padding: 0;
        margin: 0;
        width: 100%;
    }

    .score-section table {
        page-break-inside: avoid;
    }

    .info-item {
        margin-bottom: 0.5rem;
    }
}

/* 加载动画样式 */
.login-btn:disabled {
    background-color: #95a5a6;
    cursor: not-allowed;
}

.fa-spin {
    animation: fa-spin 1s infinite linear;
}

@keyframes fa-spin {
    0% {
        transform: rotate(0deg);
    }
    100% {
        transform: rotate(360deg);
    }
}

/* 个人信息区域布局 */
.info-wrapper {
    display: flex;
    justify-content: space-between;
    align-items: flex-start;
    gap: 2rem;
}

.info-main {
    flex: 1;
}

/* 添加总分颜色样式 */
.score-section td:first-child {
    color: #2ecc71;
    font-weight: bold;
}

/* 修改打印样式 */
@media print {
    .score-modal {
        position: static;
        background: none;
    }

    .score-content {
        box-shadow: none;
        max-width: 100%;
        padding: 20px;
        background-color: #f0f8ff !important; /* 淡蓝色背景 */
        -webkit-print-color-adjust: exact !important; /* Chrome/Safari */
        print-color-adjust: exact !important; /* Firefox */
    }

    .modal-buttons {
        display: none;
    }

    .personal-info {
        background-color: transparent !important;
    }

    .score-section table {
        page-break-inside: avoid;
        border-collapse: collapse;
    }

    .score-section th,
    .score-section td {
        border: 1px solid #000 !important;
    }

    /* 确保背景色和边框在打印时可见 */
    * {
        -webkit-print-color-adjust: exact !important;
        print-color-adjust: exact !important;
    }
}

/* 响应式调整 */
@media (max-width: 768px) {
    .info-wrapper {
        flex-direction: column;
    }
}

.notice-text {
    margin-top: 1rem;
    color: #666;
    font-size: 0.9rem;
    text-align: center;
    padding: 0 1rem;
    line-height: 1.5;
}

.notice-text a {
    color: #3498db;  /* 更亮的蓝色 */
    text-decoration: none;
    transition: color 0.2s;  /* 添加颜色过渡效果 */
    font-weight: 500;  /* 稍微加粗 */
}

.notice-text a:hover {
    color: #2980b9;  /* 悬停时的深蓝色 */
    text-decoration: underline;
}

/* 打印时的链接样式 */
@media print {
    .notice-text a {
        color: #000;
        text-decoration: none;
    }
} 
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>成绩查询系统</title>
    <link rel="stylesheet" href="styles.css">
    <!-- 确保引入了 Font Awesome -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/5.15.4/css/all.min.css">
</head>
<body>
    <header>
        <h1>全国大学英语四六级成绩查询系统</h1>
    </header>

    <main>
        <div class="login-container">
            <h2>考生登录</h2>
            <form id="loginForm" onsubmit="return handleLogin(event)">
                <div class="input-group">
                    <i class="fas fa-id-card"></i>
                    <input type="text" id="idNumber" placeholder="请输入证件号码" required>
                    <small>请填写你的身份证号码或准考证号</small>
                </div>

                <div class="input-group">
                    <i class="fas fa-lock"></i>
                    <input type="password" id="password" placeholder="请输入登录密码" required>
                    <small>密码需为8位以上的数字和字母组合</small>
                </div>

                <div class="input-group captcha-group">
                    <i class="fas fa-shield-alt"></i>
                    <input type="text" id="captcha" placeholder="请输入验证码" required>
                    <div class="captcha-container">
                        <canvas id="captchaCanvas"></canvas>
                        <button type="button" onclick="refreshCaptcha()" class="refresh-btn">
                            <i class="fas fa-sync-alt"></i> 换一张
                        </button>
                    </div>
                </div>

                <div class="error-message" id="errorMessage"></div>

                <button type="submit" class="login-btn">登录</button>

                <div class="links">
                    <a href="#" class="forgot-password">忘记密码？</a>
                    <a href="#" class="register">新用户注册</a>
                </div>
            </form>
        </div>
    </main>

    <footer>
        <p>© 2025 全国大学英语四六级考试管理系统</p>
        <p>联系我们：support@cet.edu.cn | 服务热线：400-123-4567</p>
    </footer>

    <script src="script.js"></script>
</body>
</html> 

- ⚡ Fun fact: ...
-->
