<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <!-- 核心修复：禁止缩放，强制宽度等于设备宽度 -->
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>嘉善厂区薪资计算器</title>
    <style>
        /* 全局重置与强制约束 */
        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            -webkit-tap-highlight-color: transparent;
        }

        html, body {
            width: 100%;
            max-width: 100vw; /* 强制最大宽度为视口宽度 */
            overflow-x: hidden; /* 彻底禁止横向滚动 */
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
            background-color: #f2f2f7;
            color: #1c1c1e;
            line-height: 1.5;
        }

        /* 主容器：自适应宽度，两侧留白较小 */
        .container {
            width: 100%;
            max-width: 600px;
            margin: 0 auto;
            padding: 12px; /* 减小两侧留白 */
            padding-bottom: 40px;
        }

        header {
            text-align: center;
            margin-bottom: 16px;
            padding-top: 8px;
        }

        h1 {
            font-size: 20px;
            font-weight: 700;
            color: #000;
            margin-bottom: 4px;
        }

        .subtitle {
            font-size: 12px;
            color: #8e8e93;
            background: rgba(0,0,0,0.05);
            display: inline-block;
            padding: 4px 10px;
            border-radius: 10px;
        }

        /* 卡片样式：圆角更小，适应移动端 */
        .card {
            background: #ffffff;
            border-radius: 12px;
            padding: 16px;
            margin-bottom: 12px;
            box-shadow: 0 1px 3px rgba(0, 0, 0, 0.05);
            border: 1px solid rgba(0,0,0,0.02);
            width: 100%; /* 确保不溢出 */
        }

        .card-header {
            display: flex;
            align-items: center;
            margin-bottom: 12px;
            border-bottom: 1px solid #f0f0f0;
            padding-bottom: 10px;
        }

        .card-header h2 {
            font-size: 16px;
            font-weight: 600;
        }

        .card-icon {
            margin-right: 6px;
            font-size: 16px;
        }

        /* 分组标题 */
        .group-title {
            font-size: 13px;
            font-weight: 600;
            color: #007aff;
            margin: 12px 0 6px 0;
            display: flex;
            align-items: center;
        }
        
        .group-title::before {
            content: '';
            display: block;
            width: 3px;
            height: 12px;
            background: #007aff;
            border-radius: 2px;
            margin-right: 5px;
        }

        /* 响应式网格布局：手机端默认单列，稍宽屏幕双列 */
        .input-grid {
            display: grid;
            grid-template-columns: 1fr; /* 默认单列，最安全 */
            gap: 10px;
            width: 100%;
        }

        /* 当屏幕宽度大于 375px (iPhone SE/8) 时尝试双列 */
        @media (min-width: 375px) {
            .input-grid {
                grid-template-columns: 1fr 1fr;
            }
            .full-width {
                grid-column: span 2;
            }
        }

        .input-item {
            position: relative;
            width: 100%; /* 确保占满格子 */
        }

        .input-item label {
            display: block;
            font-size: 12px;
            color: #8e8e93;
            margin-bottom: 4px;
            font-weight: 500;
        }

        .input-wrapper {
            position: relative;
            display: flex;
            align-items: center;
            width: 100%;
        }

        .input-icon {
            position: absolute;
            left: 8px;
            font-size: 14px;
            z-index: 1;
            opacity: 0.6;
        }

        /* 输入框样式优化 */
        input[type="number"], 
        input[type="date"],
        select {
            width: 100%;
            padding: 10px 10px 10px 32px; /* 左侧留出图标位置 */
            border: 1px solid #e5e5ea;
            border-radius: 8px;
            font-size: 16px; /* 关键：16px 防止 iOS 自动缩放页面 */
            background: #f9f9fb;
            color: #000;
            transition: all 0.2s;
            appearance: none;
            -webkit-appearance: none;
            /* 确保输入框不会撑开父容器 */
            max-width: 100%; 
        }

        input:focus {
            border-color: #007aff;
            background: #fff;
            outline: none;
            box-shadow: 0 0 0 2px rgba(0, 122, 255, 0.1);
        }

        input[readonly] {
            background: #f2f2f7;
            color: #8e8e93;
            border-color: transparent;
        }

        /* 切换开关样式 */
        .toggle-container {
            background: #e5e5ea;
            border-radius: 6px;
            padding: 2px;
            display: flex;
            margin-bottom: 12px;
            width: 100%;
        }

        .toggle-option {
            flex: 1;
            text-align: center;
            padding: 6px 0;
            font-size: 13px;
            font-weight: 600;
            color: #8e8e93;
            border-radius: 4px;
            cursor: pointer;
            transition: all 0.2s;
        }

        .toggle-option.active {
            background: #fff;
            color: #007aff;
            box-shadow: 0 1px 2px rgba(0,0,0,0.1);
        }

        /* 按钮样式 */
        .btn-calc {
            width: 100%;
            padding: 12px;
            background: #007aff;
            color: white;
            border: none;
            border-radius: 10px;
            font-size: 15px;
            font-weight: 600;
            cursor: pointer;
            margin-top: 16px;
            box-shadow: 0 2px 8px rgba(0, 122, 255, 0.2);
            transition: transform 0.1s;
        }

        .btn-calc:active {
            transform: scale(0.98);
            background: #0062cc;
        }

        /* 结果展示区 */
        .result-card {
            margin-top: 16px;
            background: #fff;
            border-radius: 10px;
            overflow: hidden;
            border: 1px solid #e5e5ea;
            animation: slideUp 0.3s ease-out;
            width: 100%;
        }

        @keyframes slideUp {
            from { opacity: 0; transform: translateY(10px); }
            to { opacity: 1; transform: translateY(0); }
        }

        .result-header {
            background: #f0f8ff;
            padding: 8px 12px;
            font-size: 13px;
            font-weight: 600;
            color: #007aff;
            border-bottom: 1px solid #e5e5ea;
        }

        .result-list {
            padding: 0;
        }

        .result-item {
            display: flex;
            justify-content: space-between;
            padding: 8px 12px;
            border-bottom: 1px dashed #f0f0f0;
            font-size: 13px;
        }

        .result-item:last-child {
            border-bottom: none;
        }

        .result-item .label {
            color: #666;
        }

        .result-item .val {
            font-weight: 500;
            color: #000;
        }

        .result-item.deduct .val {
            color: #ff3b30;
        }

        .result-total {
            background: #fafafa;
            padding: 12px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            border-top: 2px solid #007aff;
        }

        .result-total .label {
            font-weight: 700;
            color: #000;
            font-size: 14px;
        }

        .result-total .val {
            font-size: 18px;
            font-weight: 800;
            color: #007aff;
        }

        /* 底部说明 */
        .footer-info {
            background: #fff9e6;
            border-left: 3px solid #ffc107;
            padding: 10px;
            border-radius: 4px;
            font-size: 11px;
            color: #666;
            margin-top: 20px;
            line-height: 1.5;
        }

        .footer-info h4 {
            margin-bottom: 4px;
            color: #d39e00;
            font-size: 12px;
        }

        .footer-info ul {
            padding-left: 16px;
        }
    </style>
</head>
<body>

<div class="container">
    <header>
        <h1>💰 薪资与返费计算器</h1>
        <div class="subtitle">嘉善厂区专用 · 移动端适配版</div>
    </header>

    <!-- ================= 板块一：应发薪资计算 ================= -->
    <div class="card">
        <div class="card-header">
            <span class="card-icon">📊</span>
            <h2>同工同酬应发薪资</h2>
        </div>
        
        <div class="group-title">出勤与底薪</div>
        <div class="input-grid">
            <div class="input-item full-width">
                <label>固定底薪 (元)</label>
                <div class="input-wrapper">
                    <span class="input-icon">🔒</span>
                    <input type="number" id="base_salary" value="2490" readonly>
                </div>
            </div>
            <div class="input-item">
                <label>计薪天数</label>
                <div class="input-wrapper">
                    <span class="input-icon">📅</span>
                    <input type="number" id="scheduled_days" placeholder="22" inputmode="numeric">
                </div>
            </div>
            <div class="input-item">
                <label>实际出勤</label>
                <div class="input-wrapper">
                    <span class="input-icon">✅</span>
                    <input type="number" id="actual_days" placeholder="22" inputmode="numeric">
                </div>
            </div>
        </div>

        <div class="group-title">加班工时 (小时)</div>
        <div class="input-grid">
            <div class="input-item">
                <label>平时 1.5倍</label>
                <div class="input-wrapper">
                    <span class="input-icon">⏰</span>
                    <input type="number" id="ot_1" placeholder="0" step="0.5" inputmode="decimal">
                </div>
            </div>
            <div class="input-item">
                <label>周末 2.0倍</label>
                <div class="input-wrapper">
                    <span class="input-icon">📆</span>
                    <input type="number" id="ot_2" placeholder="0" step="0.5" inputmode="decimal">
                </div>
            </div>
            <div class="input-item full-width">
                <label>法定 3.0倍</label>
                <div class="input-wrapper">
                    <span class="input-icon">🇨🇳</span>
                    <input type="number" id="ot_3" placeholder="0" step="0.5" inputmode="decimal">
                </div>
            </div>
        </div>

        <div class="group-title">津贴与扣除</div>
        <div class="input-grid">
            <div class="input-item">
                <label>固定餐补</label>
                <div class="input-wrapper">
                    <span class="input-icon">🍱</span>
                    <input type="number" id="meal_allowance" value="300" readonly>
                </div>
            </div>
            <div class="input-item">
                <label>夜班天数</label>
                <div class="input-wrapper">
                    <span class="input-icon">🌙</span>
                    <input type="number" id="night_shift_days" placeholder="0" inputmode="numeric">
                </div>
            </div>
            <div class="input-item">
                <label>其他津贴</label>
                <div class="input-wrapper">
                    <span class="input-icon">➕</span>
                    <input type="number" id="other_allowance" placeholder="0" inputmode="decimal">
                </div>
            </div>
            <div class="input-item">
                <label>其他扣除</label>
                <div class="input-wrapper">
                    <span class="input-icon">➖</span>
                    <input type="number" id="other_deduction" placeholder="0" inputmode="decimal">
                </div>
            </div>
        </div>

        <button class="btn-calc" onclick="calculatePart1()">生成薪资明细</button>
        
        <!-- 结果展示区 -->
        <div id="part1-result-box" style="display:none;">
            <div class="result-card">
                <div class="result-header">📋 薪资构成明细</div>
                <div class="result-list">
                    <div class="result-item"><span class="label">折算底薪</span><span class="val" id="res-base">￥0.00</span></div>
                    <div class="result-item"><span class="label">加班费合计</span><span class="val" id="res-ot-total">￥0.00</span></div>
                    <div class="result-item"><span class="label"> ├─ 平时 (1.5x)</span><span class="val" style="font-size:12px;color:#888" id="res-ot1">￥0.00</span></div>
                    <div class="result-item"><span class="label"> ├─ 周末 (2.0x)</span><span class="val" style="font-size:12px;color:#888" id="res-ot2">￥0.00</span></div>
                    <div class="result-item"><span class="label"> └─ 法定 (3.0x)</span><span class="val" style="font-size:12px;color:#888" id="res-ot3">￥0.00</span></div>
                    <div class="result-item"><span class="label">生活补贴</span><span class="val" id="res-meal">￥300.00</span></div>
                    <div class="result-item"><span class="label">夜班津贴</span><span class="val" id="res-night">￥0.00</span></div>
                    <div class="result-item"><span class="label">其他津贴</span><span class="val" id="res-other-allow">￥0.00</span></div>
                    <div class="result-item deduct"><span class="label">其他扣除</span><span class="val" id="res-other-deduct">-￥0.00</span></div>
                </div>
                <div class="result-total">
                    <span class="label">应发总额</span>
                    <span class="val" id="part1-result">￥0.00</span>
                </div>
            </div>
        </div>
    </div>

    <!-- ================= 板块二：返费金额计算 ================= -->
    <div class="card">
        <div class="card-header">
            <span class="card-icon">🎁</span>
            <h2>返费/差价计算</h2>
        </div>
        
        <div class="toggle-container">
            <div class="toggle-option active" id="btn-package" onclick="switchMode('package')">打包价模式</div>
            <div class="toggle-option" id="btn-diff" onclick="switchMode('diff')">差价模式</div>
        </div>

        <div class="input-grid">
            <div class="input-item">
                <label>总工时 (H)</label>
                <div class="input-wrapper">
                    <span class="input-icon">⏳</span>
                    <input type="number" id="hours" inputmode="decimal" placeholder="0">
                </div>
            </div>
            <div class="input-item">
                <label>单价 (元/H)</label>
                <div class="input-wrapper">
                    <span class="input-icon">🏷️</span>
                    <input type="number" id="price" inputmode="decimal" placeholder="0">
                </div>
            </div>
        </div>

        <div class="input-item full-width" id="group-standard" style="margin-top:10px">
            <label>同工同酬应发薪资 (可手动修改)</label>
            <div class="input-wrapper">
                <span class="input-icon">💵</span>
                <input type="number" id="standard_salary" inputmode="decimal" placeholder="自动带入或手动输入">
            </div>
        </div>

        <div id="group-meal-allowance" style="margin-top:12px">
            <label style="font-size:13px;font-weight:600;margin-bottom:6px;display:block">餐补扣除方案</label>
            <div class="toggle-container">
                <div class="toggle-option active" id="btn-first-month" onclick="switchMealMode('first')">首月入职</div>
                <div class="toggle-option" id="btn-non-first-month" onclick="switchMealMode('non-first')">非首月</div>
            </div>
            
            <div id="meal-first-input">
                <div class="input-item full-width">
                    <label>正式上班日期</label>
                    <div class="input-wrapper">
                        <span class="input-icon">📅</span>
                        <input type="date" id="start_date">
                    </div>
                </div>
            </div>
            
            <div id="meal-non-first-input" style="display: none;">
                <div class="input-item full-width">
                    <label>上月已扣餐补 (0-300)</label>
                    <div class="input-wrapper">
                        <span class="input-icon">💸</span>
                        <input type="number" id="last_month_deducted" inputmode="decimal" placeholder="例如：150">
                    </div>
                </div>
            </div>
        </div>

        <button class="btn-calc" onclick="calculatePart2()">计算最终返费</button>

        <div id="part2-result-box" style="display:none; margin-top:16px;">
            <div class="result-card">
                <div class="result-header">🎉 预计到手返费</div>
                <div class="result-total" style="border-top:none; padding: 16px;">
                    <span class="label" id="result-label" style="font-size:13px;color:#666">打包差额：</span>
                    <span class="val" id="final-result" style="color:#34c759">￥0.00</span>
                </div>
            </div>
        </div>
    </div>

    <div class="footer-info">
        <h4>💡 计算规则说明</h4>
        <ul>
            <li><strong>打包价：</strong>工时×工价 - 应发薪资 - 餐补扣除</li>
            <li><strong>差价：</strong>工时×工价 - 餐补扣除</li>
            <li><strong>首月餐补：</strong>300 ÷ 当月总工作日 × 剩余可出勤天数</li>
            <li><strong>非首月：</strong>若上月未扣满300，本月补扣差额</li>
        </ul>
    </div>
</div>

<script>
    // 2026年法定节假日配置表
    const holidays2026 = {
        '2026-01-01': true, '2026-01-02': true, '2026-01-03': true, '2026-01-04': false, 
        '2026-02-16': true, '2026-02-17': true, '2026-02-18': true, '2026-02-19': true,
        '2026-02-20': true, '2026-02-21': true, '2026-02-22': true,
        '2026-02-14': false, '2026-02-28': false, 
        '2026-04-04': true, '2026-04-05': true, '2026-04-06': true,
        '2026-05-01': true, '2026-05-02': true, '2026-05-03': true, '2026-05-04': true, '2026-05-05': true,
        '2026-04-26': false, 
        '2026-06-19': true, '2026-06-20': true, '2026-06-21': true,
        '2026-09-25': true, '2026-09-26': true, '2026-09-27': true,
        '2026-10-01': true, '2026-10-02': true, '2026-10-03': true, '2026-10-04': true,
        '2026-10-05': true, '2026-10-06': true, '2026-10-07': true,
        '2026-09-27': false, '2026-10-10': false 
    };

    let currentMode = 'package'; 
    let currentMealMode = 'first'; 

    function calculatePart1() {
        const baseSalary = 2490;
        const scheduledDays = parseFloat(document.getElementById('scheduled_days').value) || 0;
        const actualDays = parseFloat(document.getElementById('actual_days').value) || 0;
        
        const ot1 = parseFloat(document.getElementById('ot_1').value) || 0;
        const ot2 = parseFloat(document.getElementById('ot_2').value) || 0;
        const ot3 = parseFloat(document.getElementById('ot_3').value) || 0;
        
        const nightShiftDays = parseFloat(document.getElementById('night_shift_days').value) || 0;
        const otherAllowance = parseFloat(document.getElementById('other_allowance').value) || 0;
        const otherDeduction = parseFloat(document.getElementById('other_deduction').value) || 0;
        const fixedMealAllowance = 300;

        const calculatedBase = scheduledDays > 0 ? (baseSalary / scheduledDays) * actualDays : 0;
        const hourlyRate = baseSalary / 21.75 / 8;
        const otPay1 = ot1 * hourlyRate * 1.5;
        const otPay2 = ot2 * hourlyRate * 2.0;
        const otPay3 = ot3 * hourlyRate * 3.0;
        const totalOtPay = otPay1 + otPay2 + otPay3;
        const nightPay = nightShiftDays * 15;
        
        const totalSalary = calculatedBase + totalOtPay + nightPay + otherAllowance - otherDeduction + fixedMealAllowance;

        // 更新UI
        document.getElementById('res-base').innerText = '￥' + calculatedBase.toFixed(2);
        document.getElementById('res-ot1').innerText = '￥' + otPay1.toFixed(2);
        document.getElementById('res-ot2').innerText = '￥' + otPay2.toFixed(2);
        document.getElementById('res-ot3').innerText = '￥' + otPay3.toFixed(2);
        document.getElementById('res-ot-total').innerText = '￥' + totalOtPay.toFixed(2);
        document.getElementById('res-meal').innerText = '￥' + fixedMealAllowance.toFixed(2);
        document.getElementById('res-night').innerText = '￥' + nightPay.toFixed(2);
        document.getElementById('res-other-allow').innerText = '￥' + otherAllowance.toFixed(2);
        document.getElementById('res-other-deduct').innerText = '-￥' + otherDeduction.toFixed(2);
        document.getElementById('part1-result').innerText = '￥' + totalSalary.toFixed(2);

        document.getElementById('part1-result-box').style.display = 'block';

        if (totalSalary > 0) {
            document.getElementById('standard_salary').value = totalSalary.toFixed(2);
        }
    }

    function switchMode(mode) {
        currentMode = mode;
        document.getElementById('btn-package').classList.toggle('active', mode === 'package');
        document.getElementById('btn-diff').classList.toggle('active', mode === 'diff');
        document.getElementById('group-standard').style.display = mode === 'package' ? 'block' : 'none';
        document.getElementById('group-meal-allowance').style.display = mode === 'package' ? 'block' : 'none';
        document.getElementById('part2-result-box').style.display = 'none';
    }

    function switchMealMode(mealMode) {
        currentMealMode = mealMode;
        document.getElementById('btn-first-month').classList.toggle('active', mealMode === 'first');
        document.getElementById('btn-non-first-month').classList.toggle('active', mealMode === 'non-first');
        document.getElementById('meal-first-input').style.display = mealMode === 'first' ? 'block' : 'none';
        document.getElementById('meal-non-first-input').style.display = mealMode === 'non-first' ? 'block' : 'none';
        document.getElementById('part2-result-box').style.display = 'none';
    }

    function getWorkdaysInMonth(year, month, startDay) {
        const daysInMonth = new Date(year, month + 1, 0).getDate();
        let workdays = 0;
        for (let d = startDay; d <= daysInMonth; d++) {
            const dateStr = `${year}-${String(month + 1).padStart(2, '0')}-${String(d).padStart(2, '0')}`;
            const dayOfWeek = new Date(year, month, d).getDay();
            if (holidays2026.hasOwnProperty(dateStr)) {
                if (holidays2026[dateStr] === false) workdays++;
                continue;
            }
            if (dayOfWeek !== 0 && dayOfWeek !== 6) workdays++;
        }
        return workdays;
    }

    function calculatePart2() {
        let hours = parseFloat(document.getElementById('hours').value) || 0;
        let price = parseFloat(document.getElementById('price').value) || 0;
        let standard = parseFloat(document.getElementById('standard_salary').value) || 0;
        
        let result = 0;
        let mealDeduction = 0; 
        
        if (currentMode === 'package') {
            result = (hours * price) - standard;
            document.getElementById('result-label').innerText = "打包价差额：";
        } else {
            result = hours * price;
            document.getElementById('result-label').innerText = "差价总额：";
        }

        if (currentMealMode === 'first') {
            const dateVal = document.getElementById('start_date').value;
            if (dateVal) {
                const [year, month, day] = dateVal.split('-').map(Number);
                const totalMonthWorkdays = getWorkdaysInMonth(year, month - 1, 1); 
                const remainWorkdays = getWorkdaysInMonth(year, month - 1, day); 
                if (totalMonthWorkdays > 0) {
                    mealDeduction = (300 / totalMonthWorkdays) * remainWorkdays;
                }
            }
        } else {
            let lastDeducted = parseFloat(document.getElementById('last_month_deducted').value) || 0;
            if (lastDeducted < 300) mealDeduction = 300 - lastDeducted;
        }
        
        result -= mealDeduction;
        document.getElementById('final-result').innerText = '￥' + result.toFixed(2);
        document.getElementById('part2-result-box').style.display = 'block';
        document.getElementById('part2-result-box').scrollIntoView({ behavior: 'smooth' });
    }
</script>

</body>
</html>
