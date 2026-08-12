<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <!-- 优化：移除 user-scalable=no，允许用户缩放查看细节 -->
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>返费差额模拟计算器</title>
    <style>
        /* 全局样式 */
        * { box-sizing: border-box; margin: 0; padding: 0; }
        body { 
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif;
            background-color: #f4f7f9; 
            color: #333; 
            padding: 20px;
            line-height: 1.6;
            /* 新增：防止iOS键盘弹起时页面异常滚动 */
            min-height: 100vh; 
            overflow-y: auto; 
        }

        /* 容器卡片 */
        .container { 
            width: 100%; /* 新增：确保小屏不溢出 */
            max-width: 480px; 
            margin: 0 auto; 
            background: #fff; 
            padding: 25px; 
            border-radius: 16px; 
            box-shadow: 0 4px 20px rgba(0,0,0,0.05);
        }

        h2 { text-align: center; margin-bottom: 25px; color: #2c3e50; font-size: 1.5rem; }
        
        /* 表单组 */
        .form-group { margin-bottom: 20px; }
        label { display: block; margin-bottom: 8px; font-weight: 600; color: #555; font-size: 0.95rem; }
        
        /* 输入框样式 */
        input[type="number"] {
            width: 100%;
            padding: 12px 15px;
            border: 1px solid #ddd;
            border-radius: 8px;
            font-size: 16px; /* 关键：iOS下必须>=16px，否则会自动缩放页面 */
            transition: border-color 0.3s;
            outline: none;
            background-color: #fafafa;
            text-align: right; 
            /* 去除数字输入框的上下箭头 */
            -moz-appearance: textfield; 
        }
        input[type="number"]::-webkit-outer-spin-button,
        input[type="number"]::-webkit-inner-spin-button {
            -webkit-appearance: none;
            margin: 0;
        }
        input[type="number"]:focus {
            border-color: #3498db;
            background-color: #fff;
            box-shadow: 0 0 0 3px rgba(52, 152, 219, 0.1);
        }

        /* 模式切换开关 */
        .mode-switch {
            display: flex;
            background: #eee;
            border-radius: 8px;
            padding: 4px;
            margin-bottom: 25px;
        }
        .mode-option {
            flex: 1;
            text-align: center;
            padding: 10px;
            cursor: pointer;
            border-radius: 6px;
            font-weight: 600;
            font-size: 0.9rem;
            transition: all 0.3s ease;
            color: #666;
            user-select: none;
        }
        .mode-option.active {
            background: #fff;
            color: #3498db;
            box-shadow: 0 2px 5px rgba(0,0,0,0.05);
        }

        /* 按钮 */
        .btn-calc {
            width: 100%;
            padding: 15px;
            background-color: #3498db;
            color: white;
            border: none;
            border-radius: 8px;
            font-size: 1.1rem;
            font-weight: bold;
            cursor: pointer;
            margin-top: 10px;
            transition: background 0.2s;
        }
        .btn-calc:active { background-color: #2980b9; transform: scale(0.98); }

        /* 结果区域 */
        .result-box {
            margin-top: 25px;
            padding: 20px;
            background-color: #e8f6fd;
            border-radius: 8px;
            text-align: center;
            display: none;
            border: 1px solid #bde0fe;
        }
        .result-title { font-size: 0.9rem; color: #555; margin-bottom: 5px; }
        .result-value { font-size: 2rem; color: #2980b9; font-weight: bold; word-break: break-all; }

        /* 底部说明 */
        .info-box { 
            margin-top: 30px; 
            padding: 15px; 
            background-color: #fdfbf7; 
            border-left: 4px solid #f1c40f; 
            border-radius: 4px; 
            font-size: 12px; 
            color: #666; 
        }
        .info-box h3 { font-size: 14px; color: #d35400; margin-bottom: 8px; margin-top: 10px; }
        .info-box h3:first-child { margin-top: 0; }
        .info-box ul { padding-left: 18px; margin-bottom: 8px; }
        .info-box li { margin-bottom: 4px; }

        /* 📱 移动端专属适配 */
        @media screen and (max-width: 480px) {
            body { padding: 10px; }
            .container { padding: 20px; border-radius: 12px; }
            h2 { font-size: 1.3rem; margin-bottom: 20px; }
            .btn-calc { padding: 14px; font-size: 1rem; }
            .result-value { font-size: 1.8rem; }
            .info-box { font-size: 11px; padding: 12px; }
        }
    </style>
</head>
<body>

<div class="container">
    <h2>返费差额模拟计算器</h2>

    <!-- 模式切换 -->
    <div class="mode-switch">
        <div class="mode-option active" id="btn-package" onclick="switchMode('package')">打包价模式</div>
        <div class="mode-option" id="btn-diff" onclick="switchMode('diff')">差价模式</div>
    </div>

    <!-- 输入区域 -->
    <div class="form-group">
        <label>请输入工时 (小时)</label>
        <input type="number" id="hours" inputmode="decimal" placeholder="例如：200.5">
    </div>

    <div class="form-group">
        <label>请输入单价 (元/小时)</label>
        <input type="number" id="price" inputmode="decimal" placeholder="例如：25.5">
    </div>

    <!-- 打包价特有输入框 -->
    <div class="form-group" id="group-standard">
        <label>同工同酬应发薪资 (元)</label>
        <input type="number" id="standard_salary" inputmode="decimal" placeholder="例如：3500">
    </div>

    <button class="btn-calc" onclick="calculateSalary()">立即计算</button>

    <!-- 结果显示 -->
    <div class="result-box" id="result-box">
        <div class="result-title" id="result-label">预计金额：</div>
        <div class="result-value" id="final-result">0.00</div>
    </div>

    <!-- 底部说明 -->
    <div class="info-box">
        <h3>同工同酬薪资架构</h3>
        <ul>
            <li>底薪：2,490元</li>
            <li>加班一：1.5倍时薪</li>
            <li>加班二：2倍时薪</li>
            <li>加班三：3倍时薪</li>
            <li>餐补：300元</li>
            <li>岗位津贴：0-800元</li>
            <li>夜班津贴：15元/天</li>
        </ul>
        <h3>特别说明</h3>
        <ul>
            <li>打包价打包同工同酬所有补贴</li>
            <li>底薪/餐补为正常5天8小时制</li>
            <li>加班一：周一至周五超过8小时的上班时间</li>
            <li>加班二：周六周日为加班二（调班除外）</li>
            <li>加班三：国家法定节假日为加班三</li>
        </ul>
        <p style="margin-top:10px; color:#999;">*本工具仅供参考，具体以财务核算为准</p>
    </div>
</div>

<script>
    let currentMode = 'package'; 
    const standardGroup = document.getElementById('group-standard'); 
    const btnPackage = document.getElementById('btn-package');
    const btnDiff = document.getElementById('btn-diff');
    
    function switchMode(mode) {
        currentMode = mode;
        if (mode === 'package') {
            btnPackage.classList.add('active');
            btnDiff.classList.remove('active');
            standardGroup.style.display = 'block';
        } else {
            btnDiff.classList.add('active');
            btnPackage.classList.remove('active');
            standardGroup.style.display = 'none';
        }
        document.getElementById('result-box').style.display = 'none';
    }

    function calculateSalary() {
        let hours = parseFloat(document.getElementById('hours').value) || 0;
        let price = parseFloat(document.getElementById('price').value) || 0;
        let standard = parseFloat(document.getElementById('standard_salary').value) || 0;
        
        let result = 0;
        const resultBox = document.getElementById('result-box');
        const resultLabel = document.getElementById('result-label');
        const resultValue = document.getElementById('final-result');

        if (currentMode === 'package') {
            let totalValue = hours * price;
            result = totalValue - standard;
            if (result < 0) result = 0; 
            resultLabel.innerText = "打包价差额：";
        } else {
            result = hours * price;
            resultLabel.innerText = "差价总额：";
        }

        resultValue.innerText = result.toFixed(2);
        resultBox.style.display = 'block';
    }
</script>
</body>
</html>
