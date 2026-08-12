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
            min-height: 100vh; 
            overflow-y: auto; 
        }

        /* 容器卡片 */
        .container { 
            width: 100%; 
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
        input[type="number"], input[type="date"] {
            width: 100%;
            padding: 12px 15px;
            border: 1px solid #ddd;
            border-radius: 8px;
            font-size: 16px; /* 关键：iOS下必须>=16px，否则会自动缩放页面 */
            transition: border-color 0.3s;
            outline: none;
            background-color: #fafafa;
            text-align: right; 
            -moz-appearance: textfield; 
        }
        input[type="number"]::-webkit-outer-spin-button,
        input[type="number"]::-webkit-inner-spin-button {
            -webkit-appearance: none;
            margin: 0;
        }
        input[type="number"]:focus, input[type="date"]:focus {
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
    <p style="margin-top:10px; color:#999;">*本工具仅供参考，具体以薪酬核算为准，仅嘉善厂区试用</p>
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

    <!-- 首月/非首月 餐补扣除选项 -->
    <div class="form-group" id="group-meal-allowance">
        <label>需要计算的时间？</label>
        <div class="mode-switch" style="margin-bottom: 15px;">
            <div class="mode-option active" id="btn-first-month" onclick="switchMealMode('first')">计算首月</div>
            <div class="mode-option" id="btn-non-first-month" onclick="switchMealMode('non-first')">计算非首月</div>
        </div>
        
        <!-- 首月输入框 (改为日期选择) -->
        <div id="meal-first-input">
            <label style="font-weight: normal; color: #777; font-size: 0.85rem;">请选择正式上班日期</label>
            <input type="date" id="start_date">
        </div>
        
        <!-- 非首月输入框 -->
        <div id="meal-non-first-input" style="display: none;">
            <label style="font-weight: normal; color: #777; font-size: 0.85rem;">上月已扣除餐补 (0-300元)</label>
            <input type="number" id="last_month_deducted" inputmode="decimal" placeholder="例如：150" min="0" max="300">
        </div>
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
	    <li>打包价计算：工时*工价-应发薪资-首月餐补</li>
	    <li>差价计算：工时*工价-首月餐补</li>
            <li>打包价包含应发薪资的所有补贴</li>
            <li>底薪为正常5天8小时制</li>
            <li>加班一：周一至周五超过8小时的上班时间</li>
            <li>加班二：周六周日为加班二（调班除外）</li>
            <li>加班三：国家法定节假日为加班三</li>
        </ul>
    </div>
</div>

<script>
    // 1. 2026年法定节假日配置表 (包含调休上班日)
    // 格式：'YYYY-MM-DD': true 表示该天是法定节假日（休息）
    // 格式：'YYYY-MM-DD': false 表示该天是调休工作日（需上班）
    const holidays2026 = {
        // 元旦
        '2026-01-01': true, '2026-01-02': true, '2026-01-03': true,
        '2026-01-04': false, // 周日调休上班
        // 春节
        '2026-02-16': true, '2026-02-17': true, '2026-02-18': true, '2026-02-19': true,
        '2026-02-20': true, '2026-02-21': true, '2026-02-22': true,
        '2026-02-14': false, '2026-02-28': false, // 周六调休上班
        // 清明节
        '2026-04-04': true, '2026-04-05': true, '2026-04-06': true,
        // 劳动节
        '2026-05-01': true, '2026-05-02': true, '2026-05-03': true, '2026-05-04': true, '2026-05-05': true,
        '2026-04-26': false, // 周日调休上班
        // 端午节
        '2026-06-19': true, '2026-06-20': true, '2026-06-21': true,
        // 中秋节
        '2026-09-25': true, '2026-09-26': true, '2026-09-27': true,
        // 国庆节
        '2026-10-01': true, '2026-10-02': true, '2026-10-03': true, '2026-10-04': true,
        '2026-10-05': true, '2026-10-06': true, '2026-10-07': true,
        '2026-09-27': false, '2026-10-10': false // 周日、周六调休上班
    };

    let currentMode = 'package'; 
    let currentMealMode = 'first'; 

    const standardGroup = document.getElementById('group-standard'); 
    const mealAllowanceGroup = document.getElementById('group-meal-allowance');
    const btnPackage = document.getElementById('btn-package');
    const btnDiff = document.getElementById('btn-diff');
    
    // 切换打包价/差价模式
    function switchMode(mode) {
        currentMode = mode;
        if (mode === 'package') {
            btnPackage.classList.add('active');
            btnDiff.classList.remove('active');
            standardGroup.style.display = 'block';
            mealAllowanceGroup.style.display = 'block'; 
        } else {
            btnDiff.classList.add('active');
            btnPackage.classList.remove('active');
            standardGroup.style.display = 'none';
            mealAllowanceGroup.style.display = 'none'; 
        }
        document.getElementById('result-box').style.display = 'none';
    }

    // 切换首月/非首月餐补模式
    function switchMealMode(mealMode) {
        currentMealMode = mealMode;
        const btnFirst = document.getElementById('btn-first-month');
        const btnNonFirst = document.getElementById('btn-non-first-month');
        const firstInput = document.getElementById('meal-first-input');
        const nonFirstInput = document.getElementById('meal-non-first-input');

        if (mealMode === 'first') {
            btnFirst.classList.add('active');
            btnNonFirst.classList.remove('active');
            firstInput.style.display = 'block';
            nonFirstInput.style.display = 'none';
        } else {
            btnNonFirst.classList.add('active');
            btnFirst.classList.remove('active');
            nonFirstInput.style.display = 'block';
            firstInput.style.display = 'none';
        }
        document.getElementById('result-box').style.display = 'none';
    }

    // 核心：计算指定月份内，从 startDay 到月底的【实际工作日】
    function getWorkdaysInMonth(year, month, startDay) {
        // 获取该月的总天数
        const daysInMonth = new Date(year, month + 1, 0).getDate();
        let workdays = 0;

        for (let d = startDay; d <= daysInMonth; d++) {
            const dateStr = `${year}-${String(month + 1).padStart(2, '0')}-${String(d).padStart(2, '0')}`;
            const dayOfWeek = new Date(year, month, d).getDay(); // 0=周日, 6=周六
            
            // 1. 优先判断是否在节假日配置表中
            if (holidays2026.hasOwnProperty(dateStr)) {
                if (holidays2026[dateStr] === false) workdays++; // 调休上班，计入
                // 如果为 true，则是法定节假日，不计入
                continue;
            }
            
            // 2. 不在配置表中的，按常规双休判断
            if (dayOfWeek !== 0 && dayOfWeek !== 6) {
                workdays++;
            }
        }
        return workdays;
    }

    // 计算薪资
    function calculateSalary() {
        let hours = parseFloat(document.getElementById('hours').value) || 0;
        let price = parseFloat(document.getElementById('price').value) || 0;
        let standard = parseFloat(document.getElementById('standard_salary').value) || 0;
        
        let result = 0;
        let mealDeduction = 0; 
        const resultBox = document.getElementById('result-box');
        const resultLabel = document.getElementById('result-label');
        const resultValue = document.getElementById('final-result');

        // 1. 计算基础差额
        if (currentMode === 'package') {
            let totalValue = hours * price;
            result = totalValue - standard;
            if (result < 0) result = 0; 
            resultLabel.innerText = "打包价差额：";
        } else {
            result = hours * price;
            resultLabel.innerText = "差价总额：";
        }

        // 2. 计算并扣除餐补 (仅在打包价模式下生效)
        if (currentMode === 'package') {
            if (currentMealMode === 'first') {
                // 首月逻辑
                const dateVal = document.getElementById('start_date').value;
                if (dateVal) {
                    const [year, month, day] = dateVal.split('-').map(Number);
                    // 计算当月总工作日（用于做除数）
                    const totalMonthWorkdays = getWorkdaysInMonth(year - 1, month - 1, 1); 
                    // 计算从入职日到月底的可出勤天数（用于做被除数）
                    const remainWorkdays = getWorkdaysInMonth(year - 1, month - 1, day); 
                    
                    if (totalMonthWorkdays > 0) {
                        mealDeduction = (300 / totalMonthWorkdays) * remainWorkdays;
                    }
                }
            } else {
                // 非首月逻辑
                let lastDeducted = parseFloat(document.getElementById('last_month_deducted').value) || 0;
                if (lastDeducted < 300) {
                    mealDeduction = 300 - lastDeducted;
                }
            }
            
            // 从返费中扣除餐补
            result -= mealDeduction;
            if (result < 0) result = 0; 
        }

        resultValue.innerText = result.toFixed(2);
        resultBox.style.display = 'block';
    }
</script>
</body>
</html>
