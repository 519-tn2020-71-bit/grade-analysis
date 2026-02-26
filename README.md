<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta name="google" content="notranslate">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <title>성적 통합 분석 시스템 v1.7</title>
    
    <!-- 라이브러리 로드 -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Pretendard:wght@400;600;700&display=swap');
        body { font-family: 'Pretendard', sans-serif; background-color: #f1f5f9; color: #334155; margin: 0; padding: 0; word-break: keep-all; }
        .card { background: white; border-radius: 1.5rem; box-shadow: 0 10px 25px -5px rgba(0,0,0,0.05); padding: 2rem; border: 1px solid rgba(226, 232, 240, 0.8); }
        .btn-indigo { background: linear-gradient(135deg, #4f46e5 0%, #3730a3 100%); color: white; transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1); }
        .btn-indigo:hover { transform: translateY(-2px); box-shadow: 0 20px 25px -5px rgba(79, 70, 229, 0.2); }
        .modal { display: none; position: fixed; inset: 0; background: rgba(15, 23, 42, 0.7); z-index: 1000; align-items: center; justify-content: center; backdrop-filter: blur(8px); }
        .grade-badge { padding: 0.25rem 0.75rem; border-radius: 9999px; font-size: 0.75rem; font-weight: 700; }
        
        @media print {
            .no-print { display: none !important; }
            body { background: white; padding: 0; }
            .card { box-shadow: none !important; border: 1px solid #eee !important; page-break-inside: avoid; margin-bottom: 2rem; }
            .print-full { width: 100% !important; grid-column: span 12 / span 12 !important; }
        }

        .custom-scrollbar::-webkit-scrollbar { width: 6px; }
        .custom-scrollbar::-webkit-scrollbar-track { background: #f1f5f9; }
        .custom-scrollbar::-webkit-scrollbar-thumb { background: #cbd5e1; border-radius: 10px; }
    </style>
</head>
<body class="p-4 md:p-10">
    <div class="max-w-6xl mx-auto">
        <!-- 상단 헤더 -->
        <header class="mb-12 flex flex-col md:flex-row justify-between items-end gap-6">
            <div>
                <div class="flex items-center gap-3 mb-2">
                    <span class="bg-indigo-600 text-white text-[10px] font-black px-2 py-1 rounded-md uppercase tracking-widest">GitHub Pages Ver.</span>
                    <h1 class="text-4xl font-black text-slate-900 tracking-tighter">성적 통합 분석 시스템</h1>
                </div>
                <p class="text-slate-500 font-medium italic">평가항목 실시간 연동 및 반별 분포 분석</p>
            </div>
            <div class="flex gap-3 no-print">
                <button id="btn-pdf" onclick="window.print()" class="hidden px-5 py-2.5 bg-white border border-slate-200 rounded-2xl text-sm font-bold text-slate-700 hover:bg-slate-50 flex items-center gap-2 shadow-sm transition-all">
                    💾 리포트 출력
                </button>
                <div class="flex bg-slate-200/50 p-1.5 rounded-2xl border border-slate-200 shadow-inner">
                    <button onclick="setMode('single')" id="btn-mode-single" class="px-6 py-2 rounded-xl text-sm font-extrabold transition-all">고사별</button>
                    <button onclick="setMode('semester')" id="btn-mode-semester" class="px-6 py-2 rounded-xl text-sm font-extrabold transition-all">학기말</button>
                </div>
            </div>
        </header>

        <div class="grid grid-cols-1 lg:grid-cols-12 gap-8">
            <!-- 설정 패널 -->
            <div class="lg:col-span-4 space-y-6 no-print">
                <div class="card bg-white/80 backdrop-blur-md border-indigo-100">
                    <h3 class="text-xs font-black text-indigo-500 uppercase tracking-widest mb-6 flex items-center gap-2">
                        <span class="w-2 h-2 bg-indigo-500 rounded-full animate-pulse"></span> Analysis Settings
                    </h3>
                    
                    <div class="space-y-5">
                        <div>
                            <label class="block text-xs font-bold text-slate-400 mb-2 ml-1">등급 산출 체계</label>
                            <select id="gradeSystem" class="w-full p-4 bg-slate-50 border border-slate-100 rounded-2xl font-bold text-sm outline-none focus:ring-2 focus:ring-indigo-500 transition-all">
                                <option value="5">상대평가 5등급제 (2025 개정)</option>
                                <option value="9">상대평가 9등급제 (기존/수능)</option>
                            </select>
                        </div>

                        <div id="semester-settings" class="hidden space-y-4 p-4 bg-indigo-50/50 rounded-2xl border border-indigo-100">
                            <div class="flex justify-between items-center mb-2">
                                <p class="text-[11px] font-black text-indigo-600 uppercase">평가 항목 및 비율 (%)</p>
                                <button onclick="addEvalItem()" class="text-[10px] bg-indigo-600 text-white px-2 py-1 rounded-md font-bold hover:bg-indigo-700">+ 추가</button>
                            </div>
                            <div id="eval-rows-container" class="space-y-2"></div>
                            <div id="weight-check-box" class="p-2 rounded-lg text-center bg-white/50 border border-indigo-100">
                                <p id="weight-check" class="text-[10px] font-bold text-indigo-600"></p>
                            </div>
                        </div>

                        <div>
                            <label class="block text-xs font-bold text-slate-400 mb-2 ml-1">파일 업로드</label>
                            <div id="file-inputs-list" class="space-y-3"></div>
                        </div>

                        <button onclick="runAnalysis()" class="w-full btn-indigo py-5 rounded-2xl font-black text-lg shadow-lg shadow-indigo-200 mt-6 active:scale-95">
                            통합 분석 시작
                        </button>
                    </div>
                </div>
            </div>

            <!-- 분석 리포트 -->
            <div class="lg:col-span-8 space-y-8 print-full">
                <div id="stats-dashboard" class="hidden grid grid-cols-1 md:grid-cols-3 gap-4">
                    <div class="card bg-slate-900 border-none text-white overflow-hidden relative">
                        <div class="relative z-10">
                            <p class="text-xs font-bold opacity-50 mb-1">전체 평균</p>
                            <p id="stat-avg" class="text-4xl font-black tracking-tight">0.00</p>
                        </div>
                        <div class="absolute -right-4 -bottom-4 w-24 h-24 bg-indigo-500/20 rounded-full blur-2xl"></div>
                    </div>
                    <div class="card text-center border-b-4 border-b-indigo-500">
                        <p class="text-xs font-bold text-slate-400 mb-1">총 인원</p>
                        <p id="stat-total" class="text-4xl font-black text-slate-800">0</p>
                    </div>
                    <div class="card text-center">
                        <p class="text-xs font-bold text-slate-400 mb-1">표준 편차</p>
                        <p id="stat-std" class="text-4xl font-black text-slate-800">0.00</p>
                    </div>
                </div>

                <div id="result-table-card" class="card hidden">
                    <h3 class="font-black text-xl text-slate-900 mb-6">📊 등급 산출 결과</h3>
                    <div class="overflow-x-auto rounded-2xl border border-slate-100 text-center">
                        <table class="w-full text-sm">
                            <thead>
                                <tr class="bg-slate-50 text-slate-400 text-[10px] uppercase font-black">
                                    <th class="p-5">등급</th>
                                    <th class="p-5">누적 비율</th>
                                    <th class="p-5">누적 인원</th>
                                    <th class="p-5">등급 컷</th>
                                    <th class="p-5 no-print">상세</th>
                                </tr>
                            </thead>
                            <tbody id="grade-table-body" class="font-bold divide-y divide-slate-50"></tbody>
                        </table>
                    </div>
                </div>

                <!-- 점수 급간별 분포 그래프 (드롭다운 포함) -->
                <div id="bar-chart-card" class="card hidden">
                    <div class="flex flex-col md:flex-row justify-between items-center mb-6 gap-4">
                        <h3 class="font-black text-slate-800 text-lg flex items-center gap-2">📈 점수 급간별 분포</h3>
                        <div class="no-print">
                            <select id="bar-scope-select" onchange="updateBarChart()" class="p-2.5 bg-slate-50 border border-slate-100 rounded-xl font-bold text-xs outline-none focus:ring-2 focus:ring-indigo-500 transition-all">
                                <option value="all">전체 분포</option>
                            </select>
                        </div>
                    </div>
                    <div class="w-full h-[300px]"><canvas id="barChart"></canvas></div>
                </div>

                <div id="chart-card" class="card hidden">
                    <div class="flex flex-col items-center">
                        <div class="w-full flex justify-between items-center mb-10 no-print">
                            <h3 class="font-black text-slate-800 text-lg">🍕 반별 등급 비율</h3>
                            <select id="pie-class-select" onchange="updatePieChart()" class="p-2.5 bg-slate-50 border border-slate-100 rounded-xl font-bold text-xs outline-none focus:ring-2 focus:ring-indigo-500 transition-all"></select>
                        </div>
                        <div class="w-full h-[350px]"><canvas id="pieChart"></canvas></div>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <!-- 명단 확인 모달 -->
    <div id="listModal" class="modal no-print" onclick="closeModal()">
        <div class="bg-white rounded-[2.5rem] p-10 max-w-xl w-full m-4 shadow-2xl overflow-hidden flex flex-col max-h-[85vh]" onclick="event.stopPropagation()">
            <div class="flex justify-between items-start mb-8">
                <div>
                    <h4 id="modalTitle" class="text-3xl font-black text-slate-900 mb-1"></h4>
                    <p id="modalSub" class="text-sm font-bold text-indigo-500"></p>
                </div>
                <button onclick="closeModal()" class="w-10 h-10 flex items-center justify-center bg-slate-50 rounded-full text-slate-400">✕</button>
            </div>
            <div id="modalContent" class="overflow-y-auto space-y-3 flex-grow pr-4 custom-scrollbar"></div>
        </div>
    </div>

    <script>
        let currentMode = 'single';
        let evalItems = [{ name: '1회고사', weight: 30 }, { name: '2회고사', weight: 30 }, { name: '수행평가', weight: 40 }];
        let masterData = {};
        let gradeBoundaries = [];
        let charts = { pie: null, bar: null };
        const COLORS = ['#4f46e5', '#6366f1', '#8b5cf6', '#a855f7', '#d946ef', '#ec4899', '#f43f5e', '#f97316', '#eab308'];

        function setMode(mode) {
            currentMode = mode;
            document.getElementById('btn-mode-single').className = mode === 'single' ? 'px-6 py-2 rounded-xl text-sm font-black bg-white text-indigo-600 shadow-sm border border-slate-200' : 'px-6 py-2 rounded-xl text-sm font-bold text-slate-400 hover:text-slate-600';
            document.getElementById('btn-mode-semester').className = mode === 'semester' ? 'px-6 py-2 rounded-xl text-sm font-black bg-white text-indigo-600 shadow-sm border border-slate-200' : 'px-6 py-2 rounded-xl text-sm font-bold text-slate-400 hover:text-slate-600';
            document.getElementById('semester-settings').classList.toggle('hidden', mode === 'single');
            refreshUI();
        }

        function addEvalItem() {
            evalItems.push({ name: '신규 평가', weight: 0 });
            refreshUI();
        }

        function removeEvalItem(index) {
            if (evalItems.length <= 1) return;
            evalItems.splice(index, 1);
            refreshUI();
        }

        function updateItemName(index, name) {
            evalItems[index].name = name;
            const labelEl = document.getElementById(`label-file-${index}`);
            if (labelEl) labelEl.innerText = `${name || '평가'} (${evalItems[index].weight}%)`;
        }

        function updateItemWeight(index, weight) {
            evalItems[index].weight = parseFloat(weight) || 0;
            const labelEl = document.getElementById(`label-file-${index}`);
            if (labelEl) labelEl.innerText = `${evalItems[index].name || '평가'} (${evalItems[index].weight}%)`;
            updateWeightCheck();
        }

        function refreshUI() {
            const container = document.getElementById('eval-rows-container');
            container.innerHTML = '';
            if (currentMode === 'semester') {
                evalItems.forEach((item, i) => {
                    const div = document.createElement('div');
                    div.className = 'flex gap-2 items-center group animate-in slide-in-from-right duration-300';
                    div.innerHTML = `<input type="text" value="${item.name}" oninput="updateItemName(${i}, this.value)" placeholder="평가명" class="w-1/2 p-2 text-[11px] font-bold border bg-white rounded-lg outline-none focus:ring-1 focus:ring-indigo-300"><div class="flex items-center w-1/2 gap-1"><input type="number" value="${item.weight}" oninput="updateItemWeight(${i}, this.value)" class="w-full p-2 text-[11px] font-black border bg-white rounded-lg outline-none text-center focus:ring-1 focus:ring-indigo-300"><button onclick="removeEvalItem(${i})" class="text-rose-400 hover:text-rose-600 p-1 opacity-0 group-hover:opacity-100 transition-opacity">✕</button></div>`;
                    container.appendChild(div);
                });
                updateWeightCheck();
            }
            const fileList = document.getElementById('file-inputs-list');
            fileList.innerHTML = '';
            if (currentMode === 'single') createFileInput(fileList, "성적 데이터 (엑셀)", "file-0");
            else evalItems.forEach((item, i) => createFileInput(fileList, `${item.name} (${item.weight}%)`, `file-${i}`));
        }

        function updateWeightCheck() {
            const total = evalItems.reduce((a,b) => a + b.weight, 0);
            const msg = document.getElementById('weight-check');
            msg.innerText = total !== 100 ? `현재 비율 합계: ${total}% (100% 미달)` : `비율 합계 완료: ${total}%`;
            msg.style.color = total !== 100 ? '#f43f5e' : '#4f46e5';
        }

        function createFileInput(container, label, id) {
            const div = document.createElement('div');
            div.innerHTML = `<p id="label-${id}" class="text-[10px] font-black text-slate-400 mb-1 ml-1 uppercase">${label}</p><input type="file" id="${id}" class="block w-full text-xs text-slate-400 file:mr-3 file:py-2 file:px-4 file:rounded-xl file:border-0 file:text-[11px] file:font-black file:bg-indigo-50 file:text-indigo-600 border border-slate-100 rounded-2xl p-1 bg-white hover:border-indigo-200 transition-all cursor-pointer">`;
            container.appendChild(div);
        }

        async function parseExcel(file) {
            return new Promise((resolve) => {
                const reader = new FileReader();
                reader.onload = (e) => {
                    const workbook = XLSX.read(new Uint8Array(e.target.result), { type: 'array' });
                    const rows = XLSX.utils.sheet_to_json(workbook.Sheets[workbook.SheetNames[0]], { header: 1 });
                    const classMap = {};
                    const numCols = Math.max(...rows.map(r => r.length));
                    for (let col = 0; col < numCols; col++) {
                        let cls = null, students = [], start = -1;
                        for (let r = 0; r < rows.length; r++) { if (typeof rows[r][col] === 'number') { cls = String(rows[r][col]); start = r + 1; break; } }
                        if (cls !== null) {
                            for (let r = start; r < rows.length; r++) {
                                const score = rows[r][col];
                                if (typeof score === 'number') students.push({ num: (col > 0 && typeof rows[r][0] === 'number' ? rows[r][0] : students.length + 1), score });
                            }
                            if (students.length > 0) classMap[cls] = students;
                        }
                    }
                    resolve(classMap);
                };
                reader.readAsArrayBuffer(file);
            });
        }

        async function runAnalysis() {
            try {
                let data = {};
                if (currentMode === 'single') {
                    const f = document.getElementById('file-0').files[0];
                    if (!f) throw "파일을 선택하세요.";
                    data = await parseExcel(f);
                } else {
                    if (evalItems.reduce((a,b)=>a+b.weight,0) !== 100) throw "합계 100%를 맞춰주세요.";
                    let temp = {};
                    for (let i = 0; i < evalItems.length; i++) {
                        const f = document.getElementById(`file-${i}`).files[0];
                        if (!f) continue;
                        const fData = await parseExcel(f);
                        const w = evalItems[i].weight / 100;
                        Object.keys(fData).forEach(c => {
                            if (!temp[c]) temp[c] = {};
                            fData[c].forEach(s => { temp[c][s.num] = (temp[c][s.num] || 0) + (s.score * w); });
                        });
                    }
                    Object.keys(temp).forEach(c => data[c] = Object.keys(temp[c]).map(n => ({ num: parseInt(n), score: temp[c][n] })));
                }
                masterData = data;
                render();
            } catch (e) { alert(e); }
        }

        function render() {
            const all = Object.values(masterData).flat().sort((a,b) => b.score - a.score);
            const total = all.length;
            if (total === 0) return;
            const avg = all.reduce((a,b)=>a+b.score,0)/total;
            const std = Math.sqrt(all.map(x=>Math.pow(x.score-avg,2)).reduce((a,b)=>a+b,0)/total);

            document.getElementById('stat-avg').innerText = avg.toFixed(2);
            document.getElementById('stat-total').innerText = `${total}명`;
            document.getElementById('stat-std').innerText = std.toFixed(2);

            const sys = document.getElementById('gradeSystem').value;
            const ratios = sys === '5' ? 
                [{r:0.1, l:'1등급'}, {r:0.34, l:'2등급'}, {r:0.66, l:'3등급'}, {r:0.90, l:'4등급'}, {r:1.0, l:'5등급'}] :
                [{r:0.04, l:'1등급'}, {r:0.11, l:'2등급'}, {r:0.23, l:'3등급'}, {r:0.4, l:'4등급'}, {r:0.6, l:'5등급'}, {r:0.77, l:'6등급'}, {r:0.89, l:'7등급'}, {r:0.96, l:'8등급'}, {r:1.0, l:'9등급'}];

            gradeBoundaries = ratios.map(g => {
                const targetIdx = Math.max(0, Math.round(total * g.r) - 1);
                return { label: g.l, ratio: (g.r * 100).toFixed(0), targetCount: Math.round(total * g.r), cut: all[targetIdx].score };
            });

            const tbody = document.getElementById('grade-table-body');
            tbody.innerHTML = '';
            gradeBoundaries.forEach((g, i) => {
                const tr = document.createElement('tr');
                tr.className = 'hover:bg-slate-50 transition-all cursor-pointer group';
                tr.onclick = () => showList(i);
                tr.innerHTML = `<td class="p-5"><span class="grade-badge bg-indigo-50 text-indigo-700 group-hover:bg-indigo-600 group-hover:text-white transition-all">${g.label}</span></td><td class="p-5 text-slate-400 text-[11px]">${g.ratio}%</td><td class="p-5 text-slate-600">${g.targetCount}명</td><td class="p-5 text-xl font-black text-slate-900 tracking-tighter">${g.cut.toFixed(2)}</td><td class="p-5 no-print text-indigo-400 text-right">➔</td>`;
                tbody.appendChild(tr);
            });

            const clsKeys = Object.keys(masterData).sort((a,b)=>parseInt(a)-parseInt(b));
            document.getElementById('bar-scope-select').innerHTML = '<option value="all">전체 분포</option>' + clsKeys.map(c => `<option value="${c}">${c}반 분포</option>`).join('');
            document.getElementById('pie-class-select').innerHTML = clsKeys.map(c => `<option value="${c}">${c}반 등급 비율</option>`).join('');
            
            ['stats-dashboard', 'result-table-card', 'bar-chart-card', 'chart-card'].forEach(id => document.getElementById(id).classList.remove('hidden'));
            document.getElementById('btn-pdf').classList.remove('hidden');
            updateBarChart();
            updatePieChart();
        }

        function updateBarChart() {
            const scope = document.getElementById('bar-scope-select').value;
            const scores = scope === 'all' ? Object.values(masterData).flat() : masterData[scope];
            const ctx = document.getElementById('barChart').getContext('2d');
            if (charts.bar) charts.bar.destroy();
            const bins = Array(10).fill(0);
            scores.forEach(s => { let idx = Math.floor(s.score / 10); if (idx >= 10) idx = 9; bins[idx]++; });
            charts.bar = new Chart(ctx, {
                type: 'bar',
                data: {
                    labels: ['0-10', '10-20', '20-30', '30-40', '40-50', '50-60', '60-70', '70-80', '80-90', '90-100'],
                    datasets: [{ label: '학생 수', data: bins, backgroundColor: scope === 'all' ? 'rgba(79, 70, 229, 0.6)' : 'rgba(16, 185, 129, 0.6)', borderColor: scope === 'all' ? '#4f46e5' : '#10b981', borderWidth: 2, borderRadius: 6 }]
                },
                options: { responsive: true, maintainAspectRatio: false, plugins: { legend: { display: false } }, scales: { y: { beginAtZero: true }, x: { grid: { display: false } } } }
            });
        }

        function updatePieChart() {
            const cls = document.getElementById('pie-class-select').value;
            const students = masterData[cls];
            const ctx = document.getElementById('pieChart').getContext('2d');
            if (charts.pie) charts.pie.destroy();
            const dist = gradeBoundaries.map((g, i) => {
                const upper = i === 0 ? 9999 : gradeBoundaries[i-1].cut;
                return students.filter(s => s.score >= g.cut && s.score < upper).length;
            });
            charts.pie = new Chart(ctx, {
                type: 'doughnut',
                data: { labels: gradeBoundaries.map(g => g.label), datasets: [{ data: dist, backgroundColor: COLORS, hoverOffset: 15, borderRadius: 8 }] },
                options: { responsive: true, maintainAspectRatio: false, plugins: { legend: { position: 'right' } } }
            });
        }

        function showList(idx) {
            const g = gradeBoundaries[idx], upper = idx === 0 ? 9999 : gradeBoundaries[idx-1].cut;
            let list = [];
            Object.keys(masterData).forEach(cls => masterData[cls].forEach(s => { if (s.score >= g.cut && s.score < upper) list.push({ cls, ...s }); }));
            list.sort((a,b) => b.score - a.score);
            document.getElementById('modalTitle').innerText = `${g.label} 명단`;
            document.getElementById('modalSub').innerText = `${g.cut.toFixed(2)}점 ~ ${upper === 9999 ? '만점' : upper.toFixed(2) + '점'}`;
            document.getElementById('modalContent').innerHTML = list.map(item => `<div class="flex justify-between p-4 bg-slate-50 rounded-2xl border border-slate-100"><span class="font-bold">${item.cls}반 ${item.num}번</span><span class="font-black text-indigo-600">${item.score.toFixed(2)}</span></div>`).join('');
            document.getElementById('listModal').style.display = 'flex';
        }

        function closeModal() { document.getElementById('listModal').style.display = 'none'; }
        window.onload = () => { setMode('single'); };
    </script>
</body>
</html>
