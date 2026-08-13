<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>综合薪资与返费计算器</title>
    <style>
        :root {
            --primary-color: #007AFF;
            --primary-light: #E6F2FF;
            --bg-color: #F5F7FA;
            --card-bg: #FFFFFF;
            --text-main: #1D1D1F;
            --text-sub: #86868B;
            --border-color: #E5E5EA;
            --success-color: #34C759;
            --danger-color: #FF3B30;
            --shadow: 0 8px 24px rgba(0, 0, 0, 0.06);
        }
        * { box-sizing: border-box; margin: 0; padding: 0; -webkit-tap-highlight-color: transparent; }
        body {
            font-family: -apple-system, BlinkMacSystemFont, "SF Pro Text", "Helvetica Neue", Arial, sans-serif;
            background-color: var(--bg-color);
            color: var(--text-main);
            padding: 20px;
            line-height: 1.5;
        }
        .container { max-width: 580px; margin: 0 auto; }
        
        /* 头部样式 */
        header { text-align: center; margin-bottom: 25px; }
        h1 { font-size: 1.6rem; font-weight: 700; color: var(--text-main); margin-bottom: 8px; letter-spacing: -0.5px; }
        .subtitle { font-size: 0.85rem; color: var(--text-sub); background: #fff; display: inline-block; padding: 4px 12px; border-radius: 20px; box-shadow: 0 2px 8px rgba(0,0,0,0.03); }

        /* 卡片通用样式 */
        .card {
            background: var(--card-bg);
            border-radius: 16px;
            padding: 24px;
            margin-bottom: 24px;
            box-shadow: var(--shadow);
            border: 1px solid rgba(0,0,0,0.02);
        }
        .card-header {
            display: flex;
            align-items: center;
            margin-bottom: 20px;
            padding-bottom: 12px;
            border-bottom: 1px solid var(--border-color);
        }
        .card-header h2 { font-size: 1.1rem; font-weight: 600; color: var(--text-main); }
        .card-icon { margin-right: 10px; font-size: 1.2rem; }

        /* 分组标题 */
        .group-title {
            font-size: 0.9rem;
            font-weight: 600;
            color: var(--primary-color);
            margin: 24px 0 12px 0;
            display: flex;
            align-items: center;
        }
        .group-title::before {
            content: '';
            display: block;
            width: 4px;
            height: 14px;
            background: var(--primary-color);
            border-radius: 2px;
            margin-right: 8px;
        }

        /* 输入框网格布局 */
        .input-grid {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 15px;
        }
        .full-width { grid-column: span 2; }

        .input-item { position: relative; }
        .input-item label {
            display: block;
            font-size: 0.8rem;
            color: var(--text-sub);
            margin-bottom: 6px;
            font-weight: 500;
        }
        .input-wrapper {
            position: relative;
            display: flex;
            align-items: center;
        }
        .input-icon {
            position: absolute;
            left: 12px;
            font-size: 1rem;
            z-index: 1;
        }
        input[type="number"], input[type="date"] {
            width: 100%;
            padding: 12px 12px 12px 38px; /* 左侧留出图标位置 */
            border: 1px solid var(--border-color);
            border-radius: 10px;
            font-size: 15px;
            background: #FAFAFA;
            color: var(--text-main);
            transition: all 0.2s;
            font-weight: 500;
        }
        input:focus {
            border-color: var(--primary-color);
            background: #fff;
            box-shadow: 0 0 0 3px rgba(0, 122, 255, 0.1);
            outline: none;
        }
        input[readonly] {
            background: #F2F2F7;
            color: var(--text-sub);
            border-color: transparent;
        }

        /* 模式切换开关 */
        .toggle-container {
            background: #F2F2F7;
            border-radius: 10px;
            padding: 4px;
            display: flex;
            margin-bottom: 20px;
        }
        .toggle-option {
            flex: 1;
            text-align: center;
            padding: 10px;
            font-size: 0.9rem;
            font-weight: 600;
            color: var(--text-sub);
            border-radius: 8px;
            cursor: pointer;
            transition: all 0.3s;
        }
        .toggle-option.active {
            background: #fff;
            color: var(--primary-color);
            box-shadow: 0 2px 6px rgba(0,0,0,0.05);
        }

        /* 按钮 */
        .btn-calc {
            width: 100%;
            padding: 16px;
            background: linear-gradient(135deg, #007AFF, #0056CC);
            color: white;
            border: none;
            border-radius: 12px;
            font-size: 1rem;
            font-weight: 600;
            cursor: pointer;
            box-shadow: 0 4px 12px rgba(0, 122, 255, 0.3);
            transition: transform 0.1s, box-shadow 0.2s;
            margin-top: 25px;
        }
        .btn-calc:active { transform: scale(0.98); box-shadow: 0 2px 6px rgba(0, 122, 255, 0.2); }

        /* 结果区域 - 账单风格 */
        .result-card {
            margin-top: 25px;
            background: #fff;
            border-radius: 12px;
            overflow: hidden;
            border: 1px solid var(--border-color);
            animation: slideDown 0.3s ease-out;
        }
        @keyframes slideDown {
            from { opacity: 0; transform: translateY(-10px); }
            to { opacity: 1; transform: translateY(0); }
        }
        .result-header {
            background: var(--primary-light);
            padding: 12px 16px;
            font-size: 0.9rem;
            font-weight: 600;
            color: var(--primary-color);
            border-bottom: 1px solid rgba(0,122,255,0.1);
        }
        .result-list { padding: 0; }
        .result-item {
            display: flex;
            justify-content: space-between;
            padding: 12px 16px;
            border-bottom: 1px dashed #eee;
            font-size: 0.95rem;
        }
        .result-item:last-child { border-bottom: none; }
        .result-item .label { color: #666; }
        .result-item .val { font-weight: 500; color: #333; }
        .result-item.deduct .val { color: var(--danger-color); }
        
        .result-total {
            background: #FAFAFA;
            padding: 16px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            border-top: 2px solid var(--primary-color);
        }
        .result-total .label { font-weight: 700; color: var(--text-main); font-size: 1rem; }
        .result-total .val { font-size: 1.4rem; font-weight: 800; color: var(--primary-color); }

        /* 底部说明 */
        .footer-info {
            background: #FFF8E1;
            border-left: 4px solid #FFC107;
            padding: 15px;
            border-radius: 4px;
            font-size: 0.8rem;
            color: #795548;
            margin-top: 30px;
        }
        .footer-info h4 { margin-bottom: 8px; color: #F57F17; }
        .footer-info ul { padding-left: 20px; }
        .footer-info li { margin-bottom: 4px; }
    </style>
</head>
<body>

<div class="container">
    <header>
        <h1>💰 薪资与返费计算器</h1>
        <div class="subtitle">嘉善厂区专用 · 仅供参考</div>
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
                <label>底薪 (元)</label>
                <div class="input-wrapper">
                    <span class="input-icon">🔒</span>
                    <input type="number" id="base_salary" value="2490" readonly>
                </div>
            </div>
            <div class="input-item">
                <label>计薪天数(当月不含周末和法定假日的总天数)</label>
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
                <label>餐补</label>
                <div class="input-wrapper">
                    <span class="input-icon">🍱</span>
                    <input type="number" id="meal_allowance" value="300" readonly>
                </div>
            </div>
            <div class="input-item">
                <label>夜班出勤天数</label>
                <div class="input-wrapper">
                    <span class="input-icon">🌙</span>
                    <input type="number" id="night_shift_days" placeholder="0" inputmode="numeric">
                </div>
            </div>
            <div class="input-item">
                <label>其他津贴(岗位津贴...)</label>
                <div class="input-wrapper">
                    <span class="input-icon">➕</span>
                    <input type="number" id="other_allowance" placeholder="0" inputmode="decimal">
                </div>
            </div>
            <div class="input-item">
                <label>其他扣除(病假/事假...)</label>
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
                    <div class="result-item"><span class="label"> ├─ 平时 (1.5x)</span><span class="val" style="font-size:0.85rem;color:#888" id="res-ot1">￥0.00</span></div>
                    <div class="result-item"><span class="label"> ├─ 周末 (2.0x)</span><span class="val" style="font-size:0.85rem;color:#888" id="res-ot2">￥0.00</span></div>
                    <div class="result-item"><span class="label"> └─ 法定 (3.0x)</span><span class="val" style="font-size:0.85rem;color:#888" id="res-ot3">￥0.00</span></div>
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

        <div class="input-item full-width" id="group-standard" style="margin-top:15px">
            <label>同工同酬应发薪资 (可手动修改)</label>
            <div class="input-wrapper">
                <span class="input-icon">💵</span>
                <input type="number" id="standard_salary" inputmode="decimal" placeholder="自动带入或手动输入">
            </div>
        </div>

        <div id="group-meal-allowance" style="margin-top:20px">
            <label style="font-size:0.9rem;font-weight:600;margin-bottom:10px;display:block">餐补扣除方案</label>
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

        <div id="part2-result-box" style="display:none; margin-top:20px;">
            <div class="result-card">
                <div class="result-header">🎉 预计到手返费</div>
                <div class="result-total" style="border-top:none; padding: 25px;">
                    <span class="label" id="result-label" style="font-size:0.9rem;color:#666">打包差额：</span>
                    <span class="val" id="final-result" style="color:var(--success-color)">￥0.00</span>
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
