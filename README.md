<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
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
        }
        /* 容器卡片 */
        .container { 
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
        input[type="text"] {
            width: 100%;
            padding: 12px 15px;
            border: 1px solid #ddd;
            border-radius: 8px;
            font-size: 16px; 
            transition: border-color 0.3s;
            outline: none;
            background-color: #fafafa;
            text-align: right; 
        }
        input[type="text"]:focus {
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
            user-select: none; /* 防止点击时选中文本 */
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
            display: none; /* 默认隐藏 */
            border: 1px solid #bde0fe;
        }
        .result-title { font-size: 0.9rem; color: #555; margin-bottom: 5px; }
        .result-value { font-size: 2rem; color: #2980b9; font-weight: bold; }

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
        .info-box h3 { font-size: 14px; color: #d35400; margin-bottom: 8px; }
        .info-box ul { padding-left: 18px; margin-bottom: 8px; }
    </style>
</head>
<body>

<div class="container">
    <h2>返费差额模拟计算器</h2>

    <!-- 模式切换 -->
    <div class="mode-switch">
        <!-- 注意：这里通过 onclick 调用 switchMode 函数 -->
        <div class="mode-option active" id="btn-package" onclick="switchMode('package')">打包价模式</div>
        <div class="mode-option" id="btn-diff" onclick="switchMode('diff')">差价模式</div>
    </div>

    <!-- 输入区域 -->
    <div class="form-group">
        <label>请输入工时 (小时)</label>
        <input type="text" id="hours" inputmode="decimal" placeholder="例如：200.5">
    </div>

    <div class="form-group">
        <label>请输入单价 (元/小时)</label>
        <input type="text" id="price" inputmode="decimal" placeholder="例如：25.5">
    </div>

    <!-- 打包价特有输入框 -->
    <!-- ID 修正为 group-standard -->
    <div class="form-group" id="group-standard">
        <label>同工同酬应发薪资 (元)</label>
        <input type="text" id="standard_salary" inputmode="decimal" placeholder="例如：3500">
    </div>

    <button class="btn-calc" onclick="calculateSalary()">立即计算</button>

    <!-- 结果显示 -->
    <div class="result-box" id="result-box">
        <div class="result-title" id="result-label">预计金额：</div>
        <div class="result-value" id="final-result">0.00</div>
    </div>

    <!-- 底部说明 -->
    <div class="info-box">
        <h3> 同工同酬薪资架构</h3>
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
    // 定义全局变量存储当前模式
    let currentMode = 'package'; 

    // 获取DOM元素
    // 修正：ID 必须与 HTML 中的 id="group-standard" 一致
    const standardGroup = document.getElementById('group-standard'); 
    const btnPackage = document.getElementById('btn-package');
    const btnDiff = document.getElementById('btn-diff');
    
    // 1. 界面联动逻辑：控制输入框显示与按钮高亮
    function switchMode(mode) {
        currentMode = mode;
        
        // 切换按钮的高亮状态
        if (mode === 'package') {
            btnPackage.classList.add('active');
            btnDiff.classList.remove('active');
            
            // 打包价模式：显示同工同酬输入框
            standardGroup.style.display = 'block';
        } else {
            btnDiff.classList.add('active');
            btnPackage.classList.remove('active');
            
            // 差价模式：隐藏同工同酬输入框
            standardGroup.style.display = 'none';
        }
        
        // 切换模式后隐藏之前的计算结果，避免混淆
        document.getElementById('result-box').style.display = 'none';
    }

    // 2. 核心计算逻辑
    function calculateSalary() {
        // 获取输入值（处理空值情况，默认为0）
        // parseFloat 会自动忽略非数字字符，确保计算安全
        let hours = parseFloat(document.getElementById('hours').value) || 0;
        let price = parseFloat(document.getElementById('price').value) || 0;
        let standard = parseFloat(document.getElementById('standard_salary').value) || 0;
        
        let result = 0;
        const resultBox = document.getElementById('result-box');
        const resultLabel = document.getElementById('result-label');
        const resultValue = document.getElementById('final-result');

        if (currentMode === 'package') {
            // --- 打包价逻辑 ---
            // 公式：打包价 = (工时 * 工价) - 同工同酬(应发)
            let totalValue = hours * price;
            result = totalValue - standard;

            // 业务保护：如果算出来是负数（比如干的活还没底薪多），通常不发钱，归零处理
            if (result < 0) {
                result = 0; 
            }
            
            resultLabel.innerText = "打包价差额：";
        } else {
            // --- 差价模式逻辑 ---
            // 公式：差价 = 工时 * 工价
            result = hours * price;
            resultLabel.innerText = "差价总额：";
        }

        // 3. 显示结果（保留2位小数）
        resultValue.innerText = result.toFixed(2);
        resultBox.style.display = 'block';
    }
</script>
</body>
</html>
