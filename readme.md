<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>嘉善厂区薪资计算器</title>
    <style>
        * { box-sizing: border-box; margin: 0; padding: 0; -webkit-tap-highlight-color: transparent; }
        body { font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif; background-color: #f2f2f7; color: #1c1c1e; line-height: 1.5; padding-bottom: 40px; }
        .container { width: 100%; max-width: 600px; margin: 0 auto; padding: 12px; }
        
        header { text-align: center; margin-bottom: 16px; padding-top: 8px; }
        h1 { font-size: 20px; font-weight: 700; color: #000; margin-bottom: 4px; }
        .subtitle { font-size: 12px; color: #8e8e93; background: rgba(0,0,0,0.05); display: inline-block; padding: 4px 10px; border-radius: 10px; }

        .card { background: #ffffff; border-radius: 12px; padding: 16px; margin-bottom: 12px; box-shadow: 0 1px 3px rgba(0, 0, 0, 0.05); border: 1px solid rgba(0,0,0,0.02); }
        .card-header { display: flex; align-items: center; margin-bottom: 12px; border-bottom: 1px solid #f0f0f0; padding-bottom: 10px; }
        .card-header h2 { font-size: 16px; font-weight: 600; }
        .card-icon { margin-right: 6px; font-size: 16px; }
        
        .group-title { font-size: 13px; font-weight: 600; color: #007aff; margin: 12px 0 6px 0; display: flex; align-items: center; }
        .group-title::before { content: ''; display: block; width: 3px; height: 12px; background: #007aff; border-radius: 2px; margin-right: 5px; }

        .input-grid { display: grid; grid-template-columns: 1fr; gap: 10px; }
        @media (min-width: 375px) {
            .input-grid { grid-template-columns: 1fr 1fr; }
            .full-width { grid-column: span 2; }
        }
        .input-item { position: relative; width: 100%; }
        
        .input-item label { display: block; font-size: 12px; color: #8e8e93; margin-bottom: 4px; font-weight: 500; }
        .highlight-text { color: #ff9500; font-weight: 700; margin-left: 2px; }

        .input-wrapper { position: relative; display: flex; align-items: center; width: 100%; }
        .input-icon { position: absolute; left: 8px; font-size: 14px; z-index: 1; opacity: 0.6; pointer-events: none; }
        
        input[type="number"], input[type="date"], input[type="month"], input[type="text"], select {
            width: 100%; padding: 10px 10px 10px 32px; border: 1px solid #e5e5ea; border-radius: 8px; font-size: 16px; background: #f9f9fb; color: #000; transition: all 0.2s; appearance: none; -webkit-appearance: none;
        }
        input:focus { border-color: #007aff; background: #fff; outline: none; box-shadow: 0 0 0 2px rgba(0, 122, 255, 0.1); }
        input[readonly] { background: #f2f2f7; color: #8e8e93; border-color: transparent; cursor: default; }

        .smart-tag { display: inline-block; font-size: 11px; padding: 2px 6px; border-radius: 4px; margin-left: 8px; font-weight: 600; }
        .tag-current { background: #e0f2fe; color: #0284c7; }
        .tag-next { background: #dcfce7; color: #16a34a; }
        .tag-other { background: #f3e8ff; color: #9333ea; }

        .btn-calc { width: 100%; padding: 12px; background: #007aff; color: white; border: none; border-radius: 10px; font-size: 15px; font-weight: 600; cursor: pointer; margin-top: 16px; box-shadow: 0 2px 8px rgba(0, 122, 255, 0.2); transition: transform 0.1s; }
        .btn-calc:active { transform: scale(0.98); background: #0062cc; }
        .btn-auto { padding: 6px 12px; background: #34c759; color: white; border: none; border-radius: 6px; font-size: 12px; font-weight: 600; cursor: pointer; margin-top: 6px; box-shadow: 0 1px 4px rgba(52, 199, 89, 0.2); }
        
        .toggle-container { background: #e5e5ea; border-radius: 6px; padding: 2px; display: flex; margin-bottom: 12px; width: 100%; }
        .toggle-option { flex: 1; text-align: center; padding: 6px 0; font-size: 13px; font-weight: 600; color: #8e8e93; border-radius: 4px; cursor: pointer; transition: all 0.2s; }
        .toggle-option.active { background: #fff; color: #007aff; box-shadow: 0 1px 2px rgba(0,0,0,0.1); }

        .result-card { margin-top: 16px; background: #fff; border-radius: 10px; overflow: hidden; border: 1px solid #e5e5ea; animation: slideUp 0.3s ease-out; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
        .result-header { background: #f0f8ff; padding: 8px 12px; font-size: 13px; font-weight: 600; color: #007aff; border-bottom: 1px solid #e5e5ea; }
        .result-list { padding: 0; }
        .result-item { display: flex; justify-content: space-between; padding: 8px 12px; border-bottom: 1px dashed #f0f0f0; font-size: 13px; }
        .result-item:last-child { border-bottom: none; }
        .result-item .label { color: #666; flex: 1; }
        .result-item .val { font-weight: 500; color: #000; text-align: right; }
        .result-total { background: #fafafa; padding: 12px; display: flex; justify-content: space-between; align-items: center; border-top: 2px solid #007aff; }
        .result-total .label { font-weight: 700; color: #000; font-size: 14px; }
        .result-total .val { font-size: 18px; font-weight: 800; color: #007aff; }

        .radio-group { display: flex; flex-direction: column; gap: 8px; width: 100%; }
        .radio-item { display: flex; align-items: center; padding: 12px; background: #f9f9fb; border: 1px solid #e5e5ea; border-radius: 8px; cursor: pointer; transition: all 0.2s; width: 100%; }
        .radio-item:active { background: #f0f0f5; }
        .radio-item input[type="radio"] { margin-right: 10px; accent-color: #007aff; width: 18px; height: 18px; }
        .radio-item span { font-size: 14px; color: #1c1c1e; font-weight: 500; }
        .radio-item.checked { border-color: #007aff; background: rgba(0, 122, 255, 0.05); }

        .info-box { margin-top: 8px; font-size: 13px; color: #666; background: #f9f9fb; padding: 10px; border-radius: 6px; border: 1px solid #f0f0f0; line-height: 1.6; }
        .info-box strong { color: #ff3b30; }

        .footer-info { background: #fff9e6; border-left: 3px solid #ffc107; padding: 10px; border-radius: 4px; font-size: 11px; color: #666; margin-top: 20px; line-height: 1.5; }
        .footer-info h4 { margin-bottom: 4px; color: #d39e00; font-size: 12px; }
        .footer-info ul { padding-left: 16px; }
    </style>
</head>
<body>

<div class="container">
    <header>
        <h1>💰 薪资与返费计算器</h1>
        <div class="subtitle">嘉善厂区专用 · 工具仅供参考</div>
    </header>

    <!-- ================= 板块一：应发薪资计算 ================= -->
    <div class="card">
        <div class="card-header">
            <span class="card-icon">📊</span>
            <h2>同工同酬应发薪资</h2>
        </div>
        
        <div class="group-title">基础信息</div>
        <div class="input-grid">
            <div class="input-item full-width">
                <label>底薪 (元)</label>
                <div class="input-wrapper">
                    <span class="input-icon">🔒</span>
                    <input type="number" id="base_salary" value="2490" readonly>
                </div>
            </div>
            <div class="input-item full-width">
                <label>开始上班日期</label>
                <div class="input-wrapper">
                    <span class="input-icon">🎓</span>
                    <input type="date" id="training_date">
                </div>
            </div>
            <div class="input-item full-width">
                <label>请选择需要计算的年份和月份</label>
                <div class="input-wrapper">
                    <span class="input-icon">🗓️</span>
                    <input type="month" id="calc_month_picker" onchange="handleMonthChange()">
                </div>
                <div style="margin-top: 4px;">
                     <span id="calc-type-badge" class="smart-tag" style="display:none"></span>
                </div>
            </div>
            
            <div class="input-item full-width">
                <label>计薪天数 <span class="highlight-text">(目标月份标准工作日)</span></label>
                <div class="input-wrapper">
                    <span class="input-icon">📅</span>
                    <input type="number" id="scheduled_days" placeholder="自动计算" readonly>
                </div>
            </div>
            <div class="input-item full-width">
                <label>实际出勤 <span class="highlight-text">(当月每周一至周五的实际出勤天数)</span></label>
                <div class="input-wrapper">
                    <span class="input-icon">✅</span>
                    <input type="number" id="actual_days" placeholder="请输入" onblur="validateActualDays()" onkeypress="if(event.keyCode==13) validateActualDays()">
                </div>
            </div>
        </div>

        <div class="group-title">加班工时 (小时)</div>
        <div class="input-grid">
            <div class="input-item">
                <label>平时 (1.5倍)</label>
                <div class="input-wrapper">
                    <span class="input-icon">⏰</span>
                    <input type="number" id="ot_1" placeholder="0" step="0.5">
                </div>
            </div>
            <div class="input-item">
                <label>周末 (2倍)</label>
                <div class="input-wrapper">
                    <span class="input-icon">📆</span>
                    <input type="number" id="ot_2" placeholder="0" step="0.5">
                </div>
            </div>
            <div class="input-item full-width">
                <label>法定 (3倍)</label>
                <div class="input-wrapper">
                    <span class="input-icon">🇨🇳</span>
                    <input type="number" id="ot_3" placeholder="0" step="0.5">
                </div>
            </div>
        </div>

        <div class="group-title">津贴</div>
        <div class="input-grid">
            <div class="input-item">
                <label>夜班出勤天数</label>
                <div class="input-wrapper">
                    <span class="input-icon">🌙</span>
                    <input type="number" id="night_shift_days" placeholder="0">
                </div>
            </div>
            <div class="input-item">
                <label>其他津贴<span class="highlight-text">(岗位津贴...)</span></label>
                <div class="input-wrapper">
                    <span class="input-icon">➕</span>
                    <input type="number" id="other_allowance" placeholder="0">
                </div>
            </div>
        </div>

        <button class="btn-calc" onclick="calculatePart1()">生成薪资明细</button>
        
        <div id="part1-result-box" style="display:none;">
            <div class="result-card">
                <div class="result-header">📋 薪资构成明细</div>
                <div class="result-list">
                    <div class="result-item"><span class="label">折算底薪</span><span class="val" id="res-base">￥0.00</span></div>
                    <div class="result-item"><span class="label">加班费合计</span><span class="val" id="res-ot-total">￥0.00</span></div>
                    <div class="result-item"><span class="label"> ├─ 平时 (1.5x)</span><span class="val" style="font-size:12px;color:#888" id="res-ot1">￥0.00</span></div>
                    <div class="result-item"><span class="label"> ├─ 周末 (2.0x)</span><span class="val" style="font-size:12px;color:#888" id="res-ot2">￥0.00</span></div>
                    <div class="result-item"><span class="label"> └─ 法定 (3.0x)</span><span class="val" style="font-size:12px;color:#888" id="res-ot3">￥0.00</span></div>
                    <div class="result-item">
                        <span class="label" id="res-meal-label">生活餐补</span>
                        <span class="val" id="res-meal">￥300.00</span>
                    </div>
                    <div class="result-item"><span class="label">夜班津贴</span><span class="val" id="res-night">￥0.00</span></div>
                    <div class="result-item"><span class="label">其他津贴</span><span class="val" id="res-other-allow">￥0.00</span></div>
                </div>
                <div class="result-total">
                    <span class="label" id="part1-result-label">应发总额</span>
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
            <div class="input-item full-width">
                <label>总工时 (H)</label>
                <div class="input-wrapper">
                    <span class="input-icon">⏳</span>
                    <input type="number" id="hours" placeholder="0" step="0.5">
                </div>
                <button class="btn-auto" onclick="autoCalcTotalHours()">🔄 重新自动计算总工时</button>
            </div>
            <div class="input-item">
                <label>工价 (元/H)</label>
                <div class="input-wrapper">
                    <span class="input-icon">🏷️</span>
                    <input type="number" id="price" placeholder="0">
                </div>
            </div>
        </div>

        <div class="input-item full-width" id="group-standard" style="margin-top:10px">
            <label>同工同酬应发薪资 <span class="highlight-text">(可手动输入)</span></label>
            <div class="input-wrapper">
                <span class="input-icon">💵</span>
                <input type="number" id="standard_salary" placeholder="自动带入或手动输入">
            </div>
        </div>

        <!-- 餐补扣除方案 (已固定引用第一板块月份) -->
        <div id="group-meal-allowance" style="margin-top:16px; border-top: 1px dashed #e5e5ea; padding-top: 16px;">
            <label style="font-size:13px;font-weight:600;margin-bottom:8px;display:block; color: #007aff;">首月餐补折算 (自动引用上方选择的需要计算月份)</label>
            
            <!-- 首月计算详情展示 -->
            <div id="meal-first-info" class="info-box" style="display: none;">
                入职首月餐补扣除计算：300元 ÷ <span id="meal-first-total-days">0</span>天(当月计薪天数) × <span id="meal-first-actual-days">0</span>天(应出勤天数) = <strong>￥<span id="meal-first-deduct">0.00</span></strong>
            </div>
            
            <!-- 次月计算详情展示 (严格遵循 300 - 入职当月扣除) -->
            <div id="meal-next-info" class="info-box" style="display: none;">
                次月餐补扣除计算：<br>
                1. 入职当月已扣除：￥<span id="meal-next-first-deduct">0.00</span><br>
                2. 次月扣除 = 300 - 入职当月扣除 = 300 - <span id="meal-next-first-deduct2">0.00</span> = <strong>￥<span id="meal-next-deduct">0.00</span></strong>
            </div>
            
            <!-- 其他月提示 -->
            <div id="meal-other-info" class="info-box" style="display: none;">
                💡 首月餐补已全部折减，不再扣除餐补。
            </div>
            
            <div id="meal-warn-info" class="info-box" style="display: none; border-color: #ff3b30; color: #ff3b30;">
                ⚠️ 请先在第一板块填写“开始上班日期”并选择“需要计算的年份和月份”。
            </div>
        </div>

        <button class="btn-calc" onclick="calculatePart2()">计算最终返费</button>

        <div id="part2-result-box" style="display:none; margin-top:16px;">
            <div class="result-card">
                <div class="result-header">🎉 预计到手返费</div>
                <div class="result-total" style="border-top:none; padding: 16px;">
                    <span class="val" id="final-result" style="color:#34c759">￥0.00</span>
                </div>
            </div>
        </div>
    </div>

    <!-- ================= 板块三：返费发放时间测算 ================= -->
    <div class="card">
        <div class="card-header">
            <span class="card-icon">🗓️</span>
            <h2>返费发放时间测算</h2>
        </div>
        
        <label style="font-size:13px;font-weight:600;margin-bottom:8px;display:block">请选择协议上的补差要求</label>
        <div class="radio-group" id="release_type_group">
            <label class="radio-item checked" onclick="selectRadio(this)">
                <input type="radio" name="release_type" value="next_month_15" checked>
                <span>次月15号在职</span>
            </label>
            <label class="radio-item" onclick="selectRadio(this)">
                <input type="radio" name="release_type" value="after_30_days">
                <span>满30天离职不受限</span>
            </label>
            <label class="radio-item" onclick="selectRadio(this)">
                <input type="radio" name="release_type" value="next_month_last_day">
                <span>次月最后一天在职</span>
            </label>
        </div>

        <button class="btn-calc" onclick="calculatePart3()">测算发放时间</button>

        <div id="part3-result-box" style="display:none; margin-top:16px;">
            <div class="result-card">
                <div class="result-header">🎉 预计返费发放日期</div>
                <div class="result-total" style="border-top:none; padding: 16px; flex-direction: column; align-items: flex-start;">
                    <div style="display:flex; justify-content:space-between; width:100%; align-items:center;">
                        <span class="label" style="font-size:14px;color:#000;font-weight:700">发放日期：</span>
                        <span class="val" id="release_date_result" style="color:#007aff; font-size:16px;">-</span>
                    </div>
                    <div style="margin-top:8px; font-size:12px; color:#8e8e93; width:100%; line-height:1.4;">
                        💡 备注：<span id="release_remark_result">-</span>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <div class="footer-info">
        <h4>💡 特别说明</h4>
        <ul>
            <li><strong>打包价：</strong>工时 × 工价 - 应发薪资 - 首月餐补折算。</li>
            <li><strong>差价：</strong>工时 × 工价 - 首月餐补折算。</li>
            <li><strong>首月餐补：</strong>标准(300元) ÷ 当月总工作日天数 × 剩余可出勤工作日天数。</li>
            <li><strong>非首月：</strong>若首月未扣满300元，次月补扣差额。</li>
            </ul>
    </div>
</div>

<script>
    // 2026年法定节假日配置表 (true为放假，false为调休上班)
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
    window.currentMealInfo = { amount: 300, included: false, text: '生活餐补' };
    window.currentMealDeduct = 0;

    function isWorkday(year, month, day) {
        const dateStr = `${year}-${String(month + 1).padStart(2, '0')}-${String(day).padStart(2, '0')}`;
        if (holidays2026.hasOwnProperty(dateStr)) {
            return holidays2026[dateStr] === false; 
        }
        const dayOfWeek = new Date(year, month, day).getDay();
        return dayOfWeek !== 0 && dayOfWeek !== 6;
    }

    function getWorkdaysInMonth(year, month) {
        const daysInMonth = new Date(year, month + 1, 0).getDate();
        let workdays = 0;
        for (let d = 1; d <= daysInMonth; d++) {
            if (isWorkday(year, month, d)) workdays++;
        }
        return workdays;
    }

    // 核心函数：计算餐补相关所有信息
    function getMealInfo(calcMonthVal, startDateVal) {
        if (!startDateVal || !calcMonthVal) return null;
        const [startYear, startMonth, startDay] = startDateVal.split('-').map(Number);
        const [calcYear, calcMonth] = calcMonthVal.split('-').map(Number);
        const jsMonth = calcMonth - 1;
        const totalWorkdays = getWorkdaysInMonth(calcYear, jsMonth);
        
        const startTotalMonths = startYear * 12 + startMonth;
        const calcTotalMonths = calcYear * 12 + calcMonth;
        
        let type = 'other';
        if (calcTotalMonths === startTotalMonths) type = 'current';
        else if (calcTotalMonths === startTotalMonths + 1) type = 'next';

        let part1Amount = 0, part1Included = false, part1Text = '';
        let part2Deduct = 0;
        let displayTotalDays = totalWorkdays;
        let displayActualDays = 0;
        let firstMonthDeductForNext = 0; // 用于次月展示

        if (type === 'current') {
            part1Amount = 300; part1Included = false;
            part1Text = '生活餐补(首月餐补已发放至工牌，不做计算)';
            
            let daysInMonth = new Date(calcYear, jsMonth + 1, 0).getDate();
            let workdaysFromStart = 0;
            for(let d = startDay; d <= daysInMonth; d++) {
                if(isWorkday(calcYear, jsMonth, d)) workdaysFromStart++;
            }
            displayActualDays = workdaysFromStart;
            part2Deduct = totalWorkdays > 0 ? (300 / totalWorkdays) * workdaysFromStart : 0;
        } else if (type === 'next') {
            // 第一板块：次月应发餐补计算
            let daysInMonth = new Date(calcYear, jsMonth + 1, 0).getDate();
            let workdaysFromNextDay = 0;
            for(let d = startDay + 1; d <= daysInMonth; d++) {
                if(isWorkday(calcYear, jsMonth, d)) workdaysFromNextDay++;
            }
            part1Amount = totalWorkdays > 0 ? (300 / totalWorkdays) * workdaysFromNextDay : 0;
            part1Included = true;
            part1Text = `生活餐补(次月折算: ${workdaysFromNextDay}天)`;
            displayActualDays = workdaysFromNextDay;
            
            // 第二板块：次月扣除逻辑 (严格遵循 300 - 入职当月扣除)
            const startJsMonth = startMonth - 1;
            const startTotalWorkdays = getWorkdaysInMonth(startYear, startJsMonth);
            let startDaysInMonth = new Date(startYear, startJsMonth + 1, 0).getDate();
            let startWorkdaysFromStart = 0;
            for(let d = startDay; d <= startDaysInMonth; d++) {
                if(isWorkday(startYear, startJsMonth, d)) startWorkdaysFromStart++;
            }
            firstMonthDeductForNext = startTotalWorkdays > 0 ? (300 / startTotalWorkdays) * startWorkdaysFromStart : 0;
            part2Deduct = 300 - firstMonthDeductForNext;
        } else {
            part1Amount = 300; part1Included = true;
            part1Text = '生活餐补';
            part2Deduct = 0;
        }

        return {
            type, calcYear, calcMonth, jsMonth, totalWorkdays,
            part1: { amount: part1Amount, included: part1Included, text: part1Text },
            part2: { deduct: part2Deduct, firstMonthDeduct: firstMonthDeductForNext },
            display: { totalDays: displayTotalDays, actualDays: displayActualDays }
        };
    }

    function handleMonthChange() {
        const startDateVal = document.getElementById('training_date').value;
        const calcMonthVal = document.getElementById('calc_month_picker').value;
        const badge = document.getElementById('calc-type-badge');

        if (!startDateVal || !calcMonthVal) {
            badge.style.display = 'none';
            return;
        }

        const [startYear, startMonth] = startDateVal.split('-').map(Number);
        const [calcYear, calcMonth] = calcMonthVal.split('-').map(Number); 

        let className = '', text = '';
        const startTotalMonths = startYear * 12 + startMonth;
        const calcTotalMonths = calcYear * 12 + calcMonth;

        if (calcTotalMonths === startTotalMonths) {
            className = 'tag-current'; text = '当月';
        } else if (calcTotalMonths === startTotalMonths + 1) {
            className = 'tag-next'; text = '次月';
        } else {
            className = 'tag-other'; text = '其他月份';
        }

        badge.className = `smart-tag ${className}`;
        badge.innerText = text;
        badge.style.display = 'inline-block';

        const mealInfo = getMealInfo(calcMonthVal, startDateVal);
        if (!mealInfo) return;

        window.currentMealInfo = mealInfo.part1;
        window.currentMealDeduct = mealInfo.part2.deduct;
        document.getElementById('scheduled_days').value = mealInfo.totalWorkdays;

        // 更新板块二 UI
        const firstInfo = document.getElementById('meal-first-info');
        const nextInfo = document.getElementById('meal-next-info');
        const otherInfo = document.getElementById('meal-other-info');
        const warnInfo = document.getElementById('meal-warn-info');
        
        firstInfo.style.display = 'none';
        nextInfo.style.display = 'none';
        otherInfo.style.display = 'none';
        warnInfo.style.display = 'none';

        if (mealInfo.type === 'current') {
            firstInfo.style.display = 'block';
            document.getElementById('meal-first-total-days').innerText = mealInfo.display.totalDays;
            document.getElementById('meal-first-actual-days').innerText = mealInfo.display.actualDays;
            document.getElementById('meal-first-deduct').innerText = mealInfo.part2.deduct.toFixed(2);
        } else if (mealInfo.type === 'next') {
            nextInfo.style.display = 'block';
            const firstDeduct = mealInfo.part2.firstMonthDeduct;
            document.getElementById('meal-next-first-deduct').innerText = firstDeduct.toFixed(2);
            document.getElementById('meal-next-first-deduct2').innerText = firstDeduct.toFixed(2);
            document.getElementById('meal-next-deduct').innerText = mealInfo.part2.deduct.toFixed(2);
        } else {
            otherInfo.style.display = 'block';
        }
    }

    document.getElementById('training_date').addEventListener('change', function() {
        if (document.getElementById('calc_month_picker').value) handleMonthChange();
    });

    function autoCalcTotalHours() {
        const actualDays = parseFloat(document.getElementById('actual_days').value) || 0;
        const ot1 = parseFloat(document.getElementById('ot_1').value) || 0;
        const ot2 = parseFloat(document.getElementById('ot_2').value) || 0;
        const ot3 = parseFloat(document.getElementById('ot_3').value) || 0;
        const totalHours = (actualDays * 8) + ot1 + ot2 + ot3;
        document.getElementById('hours').value = totalHours % 1 === 0 ? totalHours : totalHours.toFixed(1);
    }

    ['actual_days', 'ot_1', 'ot_2', 'ot_3'].forEach(id => {
        document.getElementById(id).addEventListener('input', autoCalcTotalHours);
    });

    function validateActualDays() {
        const actualInput = document.getElementById('actual_days');
        const scheduledInput = document.getElementById('scheduled_days');
        const actualVal = parseFloat(actualInput.value);
        const scheduledVal = parseFloat(scheduledInput.value);

        if (isNaN(actualVal)) return;

        if (actualVal < 1) {
            alert("实际出勤天数不能小于 1 天");
            actualInput.value = "";
            actualInput.focus();
            return;
        }

        if (scheduledVal > 0 && actualVal > scheduledVal) {
            alert(`错误：实际出勤天数 (${actualVal}) 不能大于当月的计薪总天数 (${scheduledVal})，请重新输入`);
            actualInput.value = "";
            actualInput.focus();
            return;
        }
    }

    function calculatePart1() {
        const baseSalary = parseFloat(document.getElementById('base_salary').value) || 2490;
        const scheduledDays = parseFloat(document.getElementById('scheduled_days').value) || 0;
        const actualDays = parseFloat(document.getElementById('actual_days').value) || 0;
        
        const ot1 = parseFloat(document.getElementById('ot_1').value) || 0;
        const ot2 = parseFloat(document.getElementById('ot_2').value) || 0;
        const ot3 = parseFloat(document.getElementById('ot_3').value) || 0;
        
        const nightShiftDays = parseFloat(document.getElementById('night_shift_days').value) || 0;
        const otherAllowance = parseFloat(document.getElementById('other_allowance').value) || 0;

        const calculatedBase = scheduledDays > 0 ? (baseSalary / scheduledDays) * actualDays : 0;
        const hourlyRate = baseSalary / 21.75 / 8;
        const otPay1 = ot1 * hourlyRate * 1.5;
        const otPay2 = ot2 * hourlyRate * 2.0;
        const otPay3 = ot3 * hourlyRate * 3.0;
        const totalOtPay = otPay1 + otPay2 + otPay3;
        const nightPay = nightShiftDays * 15;
        
        let totalSalary = calculatedBase + totalOtPay + nightPay + otherAllowance;

        const mealInfo = window.currentMealInfo;
        let finalTotal = totalSalary;
        if (mealInfo.included) finalTotal += mealInfo.amount;

        document.getElementById('res-base').innerText = '￥' + calculatedBase.toFixed(2);
        document.getElementById('res-ot1').innerText = '￥' + otPay1.toFixed(2);
        document.getElementById('res-ot2').innerText = '￥' + otPay2.toFixed(2);
        document.getElementById('res-ot3').innerText = '￥' + otPay3.toFixed(2);
        document.getElementById('res-ot-total').innerText = '￥' + totalOtPay.toFixed(2);
        
        document.getElementById('res-meal-label').innerText = mealInfo.text;
        document.getElementById('res-meal').innerText = '￥' + mealInfo.amount.toFixed(2);
        
        document.getElementById('res-night').innerText = '￥' + nightPay.toFixed(2);
        document.getElementById('res-other-allow').innerText = '￥' + otherAllowance.toFixed(2);
        
        document.getElementById('part1-result-label').innerText = mealInfo.included ? '应发总额 (含餐补)' : '应发总额 (不含餐补)';
        document.getElementById('part1-result').innerText = '￥' + finalTotal.toFixed(2);

        document.getElementById('part1-result-box').style.display = 'block';

        if (finalTotal > 0) {
            document.getElementById('standard_salary').value = finalTotal.toFixed(2);
        }
    }

    function switchMode(mode) {
        currentMode = mode;
        const btnPackage = document.getElementById('btn-package');
        const btnDiff = document.getElementById('btn-diff');
        
        if (mode === 'package') {
            btnPackage.classList.add('active');
            btnDiff.classList.remove('active');
        } else {
            btnDiff.classList.add('active');
            btnPackage.classList.remove('active');
        }

        document.getElementById('group-standard').style.display = mode === 'package' ? 'block' : 'none';
        document.getElementById('group-meal-allowance').style.display = mode === 'package' ? 'block' : 'none';
        document.getElementById('part2-result-box').style.display = 'none';
    }

    function calculatePart2() {
        let hours = parseFloat(document.getElementById('hours').value) || 0;
        let price = parseFloat(document.getElementById('price').value) || 0;
        let standard = parseFloat(document.getElementById('standard_salary').value) || 0;
        
        let result = 0;
        
        if (currentMode === 'package') {
            result = (hours * price) - standard;
        } else {
            result = hours * price;
        }

        const calcMonthVal = document.getElementById('calc_month_picker').value;
        const startDateVal = document.getElementById('training_date').value;
        
        if (!calcMonthVal || !startDateVal) {
            document.getElementById('meal-warn-info').style.display = 'block';
            alert('请先在第一板块填写“开始上班日期”并选择“需要计算的年份和月份”');
            return;
        }

        // 扣除餐补 (已剔除其他扣除项)
        result -= window.currentMealDeduct;
        
        document.getElementById('final-result').innerText = '￥' + result.toFixed(2);
        document.getElementById('part2-result-box').style.display = 'block';
        document.getElementById('part2-result-box').scrollIntoView({ behavior: 'smooth' });
    }

    function selectRadio(el) {
        document.querySelectorAll('#release_type_group .radio-item').forEach(item => item.classList.remove('checked'));
        el.classList.add('checked');
    }

    function calculatePart3() {
        const type = document.querySelector('input[name="release_type"]:checked').value;
        const calcMonthVal = document.getElementById('calc_month_picker').value;
        
        if (!calcMonthVal) { 
            alert('请先在第一板块选择“需要计算的年份和月份”'); 
            return; 
        }
        
        const [calcYear, calcMonth] = calcMonthVal.split('-').map(Number);
        let resultDate = new Date();
        
        // 格式化计算月份字符串 (例如: 2026年08月)
        const calcMonthStr = `${calcYear}年${String(calcMonth).padStart(2, '0')}月`;

        if (type === 'next_month_15') {
            resultDate = new Date(calcYear, calcMonth, 15); 
        } else if (type === 'after_30_days') {
            resultDate = new Date(calcYear, calcMonth, 15); 
        } else if (type === 'next_month_last_day') {
            resultDate = new Date(calcYear, calcMonth + 1, 0); 
        }
        
        const resYear = resultDate.getFullYear();
        const resMonth = String(resultDate.getMonth() + 1).padStart(2, '0');
        const resDay = String(resultDate.getDate()).padStart(2, '0');
        const resDateStr = `${resYear}年${resMonth}月${resDay}日`;
        
        // 设置新的备注逻辑
        const remarkText = `本次计算的返费为${calcMonthStr}，${resDateStr}需出勤。`;
        
        document.getElementById('release_date_result').innerText = resDateStr;
        document.getElementById('release_remark_result').innerText = remarkText;
        document.getElementById('part3-result-box').style.display = 'block';
        document.getElementById('part3-result-box').scrollIntoView({ behavior: 'smooth' });
    }
</script>

</body>
</html>
