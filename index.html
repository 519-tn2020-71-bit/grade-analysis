<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta name="google" content="notranslate">
    <title>성적 통합 분석 시스템 - 단일 파일 버전</title>
    
    <!-- 필수 라이브러리 로드 -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Pretendard:wght@400;600;700;900&display=swap');
        body { font-family: 'Pretendard', sans-serif; background-color: #f8fafc; color: #334155; }
        .card { background: white; border-radius: 1.5rem; box-shadow: 0 4px 6px -1px rgba(0,0,0,0.05); padding: 2rem; border: 1px solid #e2e8f0; }
        .btn-primary { background: #4f46e5; color: white; transition: all 0.2s; }
        .btn-primary:hover { background: #4338ca; transform: translateY(-1px); box-shadow: 0 10px 15px -3px rgba(79, 70, 229, 0.3); }
        .modal { display: none; position: fixed; inset: 0; background: rgba(15, 23, 42, 0.6); z-index: 50; align-items: center; justify-content: center; backdrop-filter: blur(4px); }
        
        @media print {
            .no-print { display: none !important; }
            body { background: white; }
            .card { box-shadow: none; border: 1px solid #eee; margin-bottom: 2rem; page-break-inside: avoid; }
            .print-full-width { grid-column: span 12 / span 12 !important; }
        }
    </style>
</head>
<body class="p-4 md:p-8">
    <div class="max-w-6xl mx-auto">
        <!-- 헤더 -->
        <header class="mb-10 flex flex-col md:flex-row justify-between items-start md:items-end gap-4 border-b border-slate-200 pb-6">
            <div>
                <span class="inline-block bg-emerald-100 text-emerald-700 text-xs font-bold px-2 py-1 rounded mb-2">GitHub Pages 완벽 호환</span>
                <h1 class="text-3xl font-black text-slate-900 tracking-tight">성적 통합 분석 시스템</h1>
                <p class="text-slate-500 mt-1">서버 없이 브라우저에서 100% 안전하게 구동됩니다.</p>
            </div>
            <div class="flex gap-2 no-print">
                <button onclick="window.print()" id="btn-print" class="hidden px-4 py-2 bg-white border border-slate-300 rounded-lg text-sm font-bold shadow-sm hover:bg-slate-50">🖨️ PDF 리포트</button>
            </div>
        </header>

        <div class="grid grid-cols-1 lg:grid-cols-12 gap-8">
            <!-- 왼쪽: 설정 패널 -->
            <div class="lg:col-span-4 space-y-6 no-print">
                <div class="card">
                    <h3 class="font-bold text-lg mb-4 flex items-center gap-2">⚙️ 분석 설정</h3>
                    
                    <div class="space-y-6">
                        <!-- 모드 선택 -->
                        <div>
                            <label class="block text-sm font-bold text-slate-700 mb-2">분석 모드</label>
                            <div class="flex gap-2 bg-slate-100 p-1 rounded-lg">
                                <button onclick="setMode('single')" id="mode-single" class="flex-1 py-2 text-sm font-bold bg-white rounded shadow-sm text-indigo-600">단일 고사</button>
                                <button onclick="setMode('semester')" id="mode-semester" class="flex-1 py-2 text-sm font-bold rounded text-slate-500 hover:text-slate-700">학기말 통합</button>
                            </div>
                        </div>

                        <!-- 등급제 선택 -->
                        <div>
                            <label class="block text-sm font-bold text-slate-700 mb-2">등급 산출 체계</label>
                            <select id="gradeSystem" class="w-full p-3 bg-slate-50 border border-slate-200 rounded-lg font-bold text-sm outline-none focus:border-indigo-500">
                                <option value="9">상대평가 9등급제 (수능형)</option>
                                <option value="5">상대평가 5등급제 (2025 개정)</option>
                            </select>
                        </div>

                        <!-- 학기말 통합 설정 영역 -->
                        <div id="semester-settings" class="hidden space-y-3 p-4 bg-indigo-50/50 rounded-lg border border-indigo-100">
                            <div class="flex justify-between items-center">
                                <label class="text-sm font-bold text-indigo-800">평가 항목 및 비율</label>
                                <button onclick="addEvalItem()" class="text-xs bg-indigo-100 text-indigo-700 px-2 py-1 rounded font-bold hover:bg-indigo-200">+ 추가</button>
                            </div>
                            <div id="eval-rows" class="space-y-2"></div>
                            <p id="weight-check" class="text-xs font-bold text-center mt-2"></p>
                        </div>

                        <!-- 파일 업로드 영역 -->
                        <div>
                            <div class="flex justify-between items-end mb-2">
                                <label class="block text-sm font-bold text-slate-700">엑셀 데이터 업로드</label>
                                <span class="text-[10px] text-slate-400">1열: 번호 / 그 외 열: 점수</span>
                            </div>
                            <div id="file-inputs" class="space-y-3"></div>
                        </div>

                        <button onclick="executeAnalysis()" class="w-full btn-primary py-4 rounded-xl font-bold text-lg shadow-md">
                            분석 실행하기
                        </button>
                    </div>
                </div>
            </div>

            <!-- 오른쪽: 결과 리포트 -->
            <div class="lg:col-span-8 space-y-6 print-full-width">
                <!-- 요약 통계 -->
                <div id="result-summary" class="hidden grid grid-cols-3 gap-4">
                    <div class="card bg-slate-800 text-white text-center p-6 border-none">
                        <p class="text-sm font-medium text-slate-400 mb-1">전체 평균</p>
                        <p id="res-avg" class="text-4xl font-black">0.00</p>
                    </div>
                    <div class="card text-center p-6">
                        <p class="text-sm font-medium text-slate-500 mb-1">총 응시 인원</p>
                        <p id="res-total" class="text-4xl font-black text-slate-800">0</p>
                    </div>
                    <div class="card text-center p-6">
                        <p class="text-sm font-medium text-slate-500 mb-1">표준 편차</p>
                        <p id="res-std" class="text-4xl font-black text-slate-800">0.00</p>
                    </div>
                </div>

                <!-- 등급 컷 테이블 -->
                <div id="result-table" class="card hidden p-0 overflow-hidden">
                    <div class="p-6 border-b border-slate-100">
                        <h3 class="font-bold text-lg text-slate-800">📊 등급 산출 결과</h3>
                    </div>
                    <div class="overflow-x-auto">
                        <table class="w-full text-center">
                            <thead class="bg-slate-50 text-slate-500 text-xs uppercase font-bold">
                                <tr>
                                    <th class="py-4">등급</th>
                                    <th class="py-4">기준 비율</th>
                                    <th class="py-4">누적 인원</th>
                                    <th class="py-4">예상 등급 컷</th>
                                    <th class="py-4 no-print">명단 확인</th>
                                </tr>
                            </thead>
                            <tbody id="grade-tbody" class="divide-y divide-slate-100 text-sm"></tbody>
                        </table>
                    </div>
                </div>

                <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                    <!-- 점수 분포 차트 -->
                    <div id="chart-bar" class="card hidden p-6">
                        <div class="flex justify-between items-center mb-4">
                            <h3 class="font-bold text-slate-800">📈 점수 분포</h3>
                            <select id="sel-bar" onchange="drawBarChart()" class="no-print p-1 bg-slate-50 border rounded text-xs font-bold"></select>
                        </div>
                        <div class="h-64"><canvas id="canvasBar"></canvas></div>
                    </div>

                    <!-- 등급 비율 차트 -->
                    <div id="chart-pie" class="card hidden p-6">
                        <div class="flex justify-between items-center mb-4">
                            <h3 class="font-bold text-slate-800">🍩 반별 등급 비율</h3>
                            <select id="sel-pie" onchange="drawPieChart()" class="no-print p-1 bg-slate-50 border rounded text-xs font-bold"></select>
                        </div>
                        <div class="h-64"><canvas id="canvasPie"></canvas></div>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <!-- 명단 모달 -->
    <div id="modal" class="modal" onclick="closeModal()">
        <div class="bg-white rounded-2xl w-full max-w-lg m-4 shadow-xl flex flex-col max-h-[80vh]" onclick="event.stopPropagation()">
            <div class="p-6 border-b border-slate-100 flex justify-between items-center bg-slate-50 rounded-t-2xl">
                <div>
                    <h4 id="modal-title" class="text-xl font-black text-slate-900"></h4>
                    <p id="modal-desc" class="text-sm font-bold text-indigo-600 mt-1"></p>
                </div>
                <button onclick="closeModal()" class="text-slate-400 hover:text-slate-600 font-bold text-xl px-2">&times;</button>
            </div>
            <div id="modal-list" class="p-6 overflow-y-auto space-y-2"></div>
        </div>
    </div>

    <script>
        // 전역 상태 변수
        let currentMode = 'single';
        let evalList = [{ name: '중간고사', weight: 40 }, { name: '기말고사', weight: 40 }, { name: '수행평가', weight: 20 }];
        let parsedData = {}; // { "1": [{num:1, score:100}, ...], "2": ... }
        let currentCuts = [];
        let chartInstances = { bar: null, pie: null };

        // 1. UI 및 모드 제어
        function setMode(mode) {
            currentMode = mode;
            document.getElementById('mode-single').className = mode === 'single' ? 'flex-1 py-2 text-sm font-bold bg-white rounded shadow-sm text-indigo-600' : 'flex-1 py-2 text-sm font-bold rounded text-slate-500 hover:text-slate-700';
            document.getElementById('mode-semester').className = mode === 'semester' ? 'flex-1 py-2 text-sm font-bold bg-white rounded shadow-sm text-indigo-600' : 'flex-1 py-2 text-sm font-bold rounded text-slate-500 hover:text-slate-700';
            document.getElementById('semester-settings').style.display = mode === 'single' ? 'none' : 'block';
            renderSettingsUI();
        }

        function renderSettingsUI() {
            // 평가 항목 렌더링
            const rowContainer = document.getElementById('eval-rows');
            rowContainer.innerHTML = '';
            
            if (currentMode === 'semester') {
                evalList.forEach((item, idx) => {
                    rowContainer.innerHTML += `
                        <div class="flex gap-2 items-center">
                            <input type="text" value="${item.name}" onchange="updateEval(${idx}, 'name', this.value)" class="flex-1 p-2 text-xs border rounded outline-none font-bold" placeholder="항목명">
                            <input type="number" value="${item.weight}" onchange="updateEval(${idx}, 'weight', this.value)" class="w-16 p-2 text-xs border rounded outline-none text-center font-bold" placeholder="비율(%)">
                            <button onclick="removeEvalItem(${idx})" class="text-red-400 font-bold px-2 hover:text-red-600">&times;</button>
                        </div>`;
                });
                checkWeights();
            }

            // 파일 입력창 렌더링
            const fileContainer = document.getElementById('file-inputs');
            fileContainer.innerHTML = '';
            
            if (currentMode === 'single') {
                fileContainer.innerHTML = `<input type="file" id="file-0" accept=".xlsx, .xls" class="block w-full text-xs text-slate-500 file:mr-4 file:py-2 file:px-4 file:rounded-md file:border-0 file:text-xs file:font-bold file:bg-indigo-50 file:text-indigo-700 border border-slate-200 p-1 rounded-lg">`;
            } else {
                evalList.forEach((item, idx) => {
                    fileContainer.innerHTML += `
                        <div class="mb-2">
                            <span class="text-xs font-bold text-slate-500 mb-1 block">${item.name} 데이터 (${item.weight}%)</span>
                            <input type="file" id="file-${idx}" accept=".xlsx, .xls" class="block w-full text-xs text-slate-500 file:mr-4 file:py-2 file:px-4 file:rounded-md file:border-0 file:text-xs file:font-bold file:bg-indigo-50 file:text-indigo-700 border border-slate-200 p-1 rounded-lg">
                        </div>`;
                });
            }
        }

        function addEvalItem() { evalList.push({ name: '새 항목', weight: 0 }); renderSettingsUI(); }
        function removeEvalItem(idx) { if (evalList.length > 1) { evalList.splice(idx, 1); renderSettingsUI(); } }
        function updateEval(idx, key, val) { evalList[idx][key] = key === 'weight' ? Number(val) : val; checkWeights(); renderSettingsUI(); }
        
        function checkWeights() {
            const sum = evalList.reduce((acc, cur) => acc + cur.weight, 0);
            const msgObj = document.getElementById('weight-check');
            msgObj.textContent = sum === 100 ? "✅ 비율 합계 100% 확인 완료" : `⚠️ 현재 합계: ${sum}% (100%를 맞춰주세요)`;
            msgObj.className = sum === 100 ? "text-xs font-bold text-center mt-2 text-emerald-600" : "text-xs font-bold text-center mt-2 text-red-500";
            return sum === 100;
        }

        // 2. 엑셀 파싱 로직 (클라이언트 사이드 전용)
        function readExcelFile(file) {
            return new Promise((resolve, reject) => {
                if (!file) return resolve(null);
                const reader = new FileReader();
                reader.onload = (e) => {
                    try {
                        const data = new Uint8Array(e.target.result);
                        const workbook = XLSX.read(data, { type: 'array' });
                        const sheet = workbook.Sheets[workbook.SheetNames[0]];
                        const json = XLSX.utils.sheet_to_json(sheet, { header: 1 }); // 2차원 배열 형태
                        
                        let classData = {};
                        
                        // 데이터 탐색: 첫 번째 열이 번호, 나머지 열이 특정 반의 점수라고 가정
                        // 헤더(반 이름) 찾기
                        let headerRowIdx = -1;
                        for(let i=0; i<Math.min(10, json.length); i++) {
                            if(json[i] && json[i].length > 1 && json[i].some(v => typeof v === 'string' && (v.includes('반') || v.includes('class') || !isNaN(v)))) {
                                headerRowIdx = i; break;
                            }
                        }

                        // 헤더가 명확하지 않으면 0번째 행을 헤더로 간주
                        if(headerRowIdx === -1) headerRowIdx = 0;

                        // 열별로 반 데이터 추출
                        for (let col = 1; col < json[headerRowIdx].length; col++) {
                            let className = String(json[headerRowIdx][col] || col); // 반 이름
                            if(!className || className.trim() === '') continue;
                            
                            let students = [];
                            for (let row = headerRowIdx + 1; row < json.length; row++) {
                                let score = parseFloat(json[row][col]);
                                let num = parseInt(json[row][0]);
                                
                                if (!isNaN(score)) {
                                    students.push({ num: isNaN(num) ? row : num, score: score });
                                }
                            }
                            if (students.length > 0) classData[className] = students;
                        }
                        resolve(classData);
                    } catch (err) {
                        reject("엑셀 파일을 읽는 중 오류가 발생했습니다. 양식을 확인해주세요.");
                    }
                };
                reader.onerror = () => reject("파일 읽기 실패");
                reader.readAsArrayBuffer(file);
            });
        }

        // 3. 메인 분석 실행
        async function executeAnalysis() {
            try {
                let mergedData = {};

                if (currentMode === 'single') {
                    const fileInput = document.getElementById('file-0');
                    if (!fileInput.files.length) return alert("엑셀 파일을 업로드해주세요.");
                    mergedData = await readExcelFile(fileInput.files[0]);
                } else {
                    if (!checkWeights()) return alert("평가 항목의 비율 합계를 100%로 맞춰주세요.");
                    
                    let tempDB = {}; // { "반": { "번호": 누적점수 } }
                    
                    for (let i = 0; i < evalList.length; i++) {
                        const fileInput = document.getElementById(`file-${i}`);
                        if (!fileInput.files.length) continue; // 업로드 안 된 파일은 무시 (0점 처리 아님)
                        
                        const fileData = await readExcelFile(fileInput.files[0]);
                        if(!fileData) continue;

                        const weight = evalList[i].weight / 100;
                        
                        Object.keys(fileData).forEach(className => {
                            if (!tempDB[className]) tempDB[className] = {};
                            fileData[className].forEach(student => {
                                if (!tempDB[className][student.num]) tempDB[className][student.num] = 0;
                                tempDB[className][student.num] += (student.score * weight);
                            });
                        });
                    }

                    // tempDB를 { "1": [{num:1, score:100}], ... } 형태로 변환
                    Object.keys(tempDB).forEach(className => {
                        mergedData[className] = Object.keys(tempDB[className]).map(num => ({
                            num: parseInt(num),
                            score: tempDB[className][num]
                        }));
                    });
                }

                if (Object.keys(mergedData).length === 0) {
                    return alert("분석할 유효한 데이터가 없습니다. 엑셀 파일 형식을 확인해주세요.");
                }

                parsedData = mergedData;
                calculateAndRender();

            } catch (error) {
                alert(error);
            }
        }

        // 4. 등급 계산 및 화면 출력
        function calculateAndRender() {
            // 모든 점수 추출 및 정렬
            let allScores = [];
            Object.keys(parsedData).forEach(cls => {
                parsedData[cls].forEach(s => allScores.push(s.score));
            });
            allScores.sort((a, b) => b - a);
            
            const totalCount = allScores.length;
            const avg = allScores.reduce((a, b) => a + b, 0) / totalCount;
            const variance = allScores.reduce((a, b) => a + Math.pow(b - avg, 2), 0) / totalCount;
            const std = Math.sqrt(variance);

            // 통계 요약 갱신
            document.getElementById('res-avg').textContent = avg.toFixed(2);
            document.getElementById('res-total').textContent = totalCount;
            document.getElementById('res-std').textContent = std.toFixed(2);

            // 등급 컷 계산
            const gradeType = document.getElementById('gradeSystem').value;
            const ratios = gradeType === '5' 
                ? [0.10, 0.34, 0.66, 0.90, 1.00] 
                : [0.04, 0.11, 0.23, 0.40, 0.60, 0.77, 0.89, 0.96, 1.00];

            currentCuts = [];
            const tbody = document.getElementById('grade-tbody');
            tbody.innerHTML = '';

            ratios.forEach((ratio, idx) => {
                const targetCount = Math.round(totalCount * ratio);
                const cutIdx = Math.min(targetCount - 1, allScores.length - 1);
                const cutScore = cutIdx >= 0 ? allScores[cutIdx] : 0;
                
                currentCuts.push({
                    grade: idx + 1,
                    cumCount: targetCount,
                    cutScore: cutScore
                });

                // 테이블 행 추가
                const tr = document.createElement('tr');
                tr.className = "hover:bg-slate-50 transition-colors cursor-pointer group";
                tr.onclick = () => showModal(idx + 1);
                tr.innerHTML = `
                    <td class="py-3 font-black text-indigo-600">${idx + 1}등급</td>
                    <td class="py-3 text-slate-500">${(ratio * 100).toFixed(0)}%</td>
                    <td class="py-3 font-bold text-slate-700">${targetCount}명</td>
                    <td class="py-3 font-black text-lg">${cutScore.toFixed(2)}</td>
                    <td class="py-3 text-indigo-400 no-print group-hover:text-indigo-600 font-bold">확인 &rarr;</td>
                `;
                tbody.appendChild(tr);
            });

            // 셀렉트 박스 갱신 및 차트 그리기
            const classKeys = Object.keys(parsedData).sort((a, b) => {
                // 숫자형 반이름 정렬
                let numA = parseInt(a), numB = parseInt(b);
                if(!isNaN(numA) && !isNaN(numB)) return numA - numB;
                return a.localeCompare(b);
            });

            const selBar = document.getElementById('sel-bar');
            selBar.innerHTML = '<option value="all">전체 학년</option>' + classKeys.map(c => `<option value="${c}">${c}반</option>`).join('');
            
            const selPie = document.getElementById('sel-pie');
            selPie.innerHTML = classKeys.map(c => `<option value="${c}">${c}반</option>`).join('');

            // UI 보이기
            document.getElementById('result-summary').classList.remove('hidden');
            document.getElementById('result-table').classList.remove('hidden');
            document.getElementById('chart-bar').classList.remove('hidden');
            document.getElementById('chart-pie').classList.remove('hidden');
            document.getElementById('btn-print').classList.remove('hidden');

            drawBarChart();
            drawPieChart();
        }

        // 5. 차트 그리기 로직
        function drawBarChart() {
            const target = document.getElementById('sel-bar').value;
            let scores = [];
            if (target === 'all') {
                Object.values(parsedData).forEach(arr => arr.forEach(s => scores.push(s.score)));
            } else {
                scores = parsedData[target].map(s => s.score);
            }

            // 히스토그램 데이터 만들기 (10점 단위)
            let bins = Array(10).fill(0);
            scores.forEach(s => {
                let idx = Math.floor(s / 10);
                if (idx >= 10) idx = 9; // 100점은 마지막 구간에 포함
                if (idx < 0) idx = 0;
                bins[idx]++;
            });

            if (chartInstances.bar) chartInstances.bar.destroy();
            
            const ctx = document.getElementById('canvasBar').getContext('2d');
            chartInstances.bar = new Chart(ctx, {
                type: 'bar',
                data: {
                    labels: ['0-10', '10-20', '20-30', '30-40', '40-50', '50-60', '60-70', '70-80', '80-90', '90-100'],
                    datasets: [{
                        label: '학생 수',
                        data: bins,
                        backgroundColor: '#6366f1',
                        borderRadius: 4
                    }]
                },
                options: { maintainAspectRatio: false, plugins: { legend: { display: false } }, scales: { y: { beginAtZero: true } } }
            });
        }

        function drawPieChart() {
            const target = document.getElementById('sel-pie').value;
            const students = parsedData[target] || [];
            
            // 등급별 인원 수 계산
            let gradeCounts = Array(currentCuts.length).fill(0);
            students.forEach(student => {
                for (let i = 0; i < currentCuts.length; i++) {
                    if (student.score >= currentCuts[i].cutScore) {
                        gradeCounts[i]++;
                        break;
                    }
                }
            });

            if (chartInstances.pie) chartInstances.pie.destroy();

            const ctx = document.getElementById('canvasPie').getContext('2d');
            chartInstances.pie = new Chart(ctx, {
                type: 'doughnut',
                data: {
                    labels: currentCuts.map(c => `${c.grade}등급`),
                    datasets: [{
                        data: gradeCounts,
                        backgroundColor: ['#4f46e5','#6366f1','#8b5cf6','#a855f7','#d946ef','#ec4899','#f43f5e','#f97316','#eab308'],
                        borderWidth: 0
                    }]
                },
                options: { maintainAspectRatio: false, plugins: { legend: { position: 'right' } } }
            });
        }

        // 6. 명단 모달 로직
        function showModal(targetGrade) {
            const cutData = currentCuts[targetGrade - 1];
            const lowerBound = cutData.cutScore;
            const upperBound = targetGrade === 1 ? Infinity : currentCuts[targetGrade - 2].cutScore;

            let targetStudents = [];
            Object.keys(parsedData).forEach(className => {
                parsedData[className].forEach(student => {
                    if (student.score >= lowerBound && student.score < upperBound) {
                        targetStudents.push({ cls: className, num: student.num, score: student.score });
                    }
                });
            });

            targetStudents.sort((a, b) => b.score - a.score);

            document.getElementById('modal-title').textContent = `${targetGrade}등급 명단`;
            document.getElementById('modal-desc').textContent = `컷 점수: ${lowerBound.toFixed(2)}점 이상`;
            
            const listHtml = targetStudents.map(s => `
                <div class="flex justify-between items-center bg-slate-50 p-3 rounded-lg border border-slate-100">
                    <span class="font-bold text-slate-700">${s.cls}반 ${s.num}번</span>
                    <span class="font-black text-indigo-600">${s.score.toFixed(2)}점</span>
                </div>
            `).join('');
            
            document.getElementById('modal-list').innerHTML = listHtml || '<p class="text-center text-slate-400 py-4">해당 등급 인원이 없습니다.</p>';
            document.getElementById('modal').style.display = 'flex';
        }

        function closeModal() {
            document.getElementById('modal').style.display = 'none';
        }

        // 초기화
        window.onload = () => setMode('single');
    </script>
</body>
</html>
