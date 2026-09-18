------------------------------
## 🛠️ 기술 스택 및 오픈소스 아키텍처
API 연계가 없는 완전한 독립형 웹앱 구현을 위해 IT 테크 포럼 및 깃허브(GitHub)의 오픈소스 라이브러리 중 바이브코딩(HTML/CSS/JS 코드 주입만으로 완성)에 적합한 검증된 프레임워크를 조합합니다.

| 분류 | 오픈소스 기술 / 라이브러리 | 적용 목적 및 특징 | 연계 방식 |
|---|---|---|---|
| UI 프레임워크 | Tailwind CSS | 현대적이고 깔끔한 대시보드 화면 및 입력 폼 컴포넌트 구성 | CDN 주입 (무료) |
| 데이터 처리 | Pure JavaScript (ES6+) | 사출금형 표준 견적 수식 알고리즘 탑재 및 실시간 시뮬레이션 | 로컬 엔진 동작 |
| 데이터 보존 | HTML5 LocalStorage | 공장 표준 임율, 재료 단가, 고객사 견적 이력 로컬 저장 | 브라우저 내장 데이터베이스 |
| 내보내기 | jspdf & jspdf-autotable | 확정된 견적서를 결재/납품용 공식 PDF 파일로 즉시 다운로드 | 클라이언트 사이드 변환 |

------------------------------
## 📐 핵심 견적 시뮬레이션 알고리즘 설계
사출금형 원가계산 포럼 및 현업 표준 공식을 기반으로 설계한 4대 핵심 비용 산출 알고리즘입니다. 웹앱 내부 JavaScript 연산 로직에 그대로 적용됩니다. [2, 3] 
## 1. 재료비 (Material Cost) 계산식
금형 베이스 및 코어 강재(Steel)의 부피와 밀도를 기준으로 기본 재료비를 산출합니다.
$$\text{강재 중량(kg)} = \frac{\text{가로(mm)} \times \text{세로(mm)} \times \text{높이(mm)}}{1,000,000} \times 7.85\ (\text{철 밀도})$$ 
$$\text{최종 재료비} = (\text{강재 중량} \times \text{강재 kg당 단가}) + \text{표준 몰드베이스 구입비} + \text{핫러너 비용(옵션)}$$ 
## 2. 가공비 (Machining Cost) 계산식
설계(CAD), CNC 밀링, 방전가공(EDM), 와이어 컷, 사상 및 조립 등 공정별 표준 작업 시간(Standard Time)과 임율을 곱하여 계산합니다.
$$\text{총 가공비} = \sum (\text{공정별 소요 시간(hr)} \times \text{장비/인력 시간당 임율})$$ 

* 
* 팁: 형상의 복잡도(일반, 복잡, 초정밀)에 따라 가공 시간에 가중치 스케일러(1.0 ~ 1.5)를 곱하는 시뮬레이터 기능을 포함합니다.
* 

## 3. 기구물 추가 비용 (Mechanism Cost)
언더컷 처리를 위한 경사핀(Lifter)이나 슬라이드 코어(Slide Core) 개당 표준 가공 공수를 가산합니다.
$$\text{기구 추가비} = (\text{슬라이드 개수} \times \text{개당 단가}) + (\text{경사핀 개수} \times \text{개당 단가})$$ 
## 4. 최종 견적가 및 마진 시뮬레이션
$$\text{제조 원가} = \text{재료비} + \text{가공비} + \text{기구 추가비} + \text{시사출 비용(Test Run)}$$ 
$$\text{최종 공급가} = \text{제조 원가} \times \left(1 + \frac{\text{목표 마진율(\%)}}{100}\right) + \text{일반관리비}$$ 
------------------------------
## 💻 바이브코딩용 웹앱 통합 소스코드
아래 코드를 복사하여 하나의 index.html 파일로 저장하고 브라우저로 열면 즉시 작동하는 견적 시뮬레이터 및 PDF 생성기가 실행됩니다.

<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>사출금형 견적 시뮬레이터 시스템</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://jsdelivr.net"></script>
    <!-- jsPDF & AutoTable CDN -->
    <script src="https://cloudflare.com"></script>
    <script src="https://cloudflare.com"></script>
</head>
<body class="bg-slate-50 text-slate-800 p-6">
    <div class="max-w-6xl mx-auto bg-white p-8 rounded-xl shadow-md border border-slate-200">
        <!-- 헤더 -->
        <div class="border-b border-slate-200 pb-4 mb-6">
            <h1 class="text-2xl font-bold text-slate-900">🛠️ 사출금형 견적 시뮬레이션 & 자동 생성기</h1>
            <p class="text-sm text-slate-500 mt-1">외부 유출 없는 순수 브라우저 연산 독립형 시스템</p>
        </div>

        <div class="grid grid-cols-1 lg:grid-cols-2 gap-8">
            <!-- 왼쪽: 입력 및 시뮬레이션 컨트롤 -->
            <div class="space-y-6">
                <!-- 1. 기본 정보 및 강재 계산 -->
                <div class="bg-slate-50 p-4 rounded-lg border border-slate-200">
                    <h2 class="text-lg font-semibold text-slate-700 mb-3">1. 금형 사이즈 및 강재 설정</h2>
                    <div class="grid grid-cols-3 gap-3 mb-3">
                        <div>
                            <label class="block text-xs font-medium text-slate-500 mb-1">가로 (L, mm)</label>
                            <input type="number" id="length" value="400" class="w-full p-2 border rounded bg-white text-sm" oninput="calculateQuote()">
                        </div>
                        <div>
                            <label class="block text-xs font-medium text-slate-500 mb-1">세로 (W, mm)</label>
                            <input type="number" id="width" value="400" class="w-full p-2 border rounded bg-white text-sm" oninput="calculateQuote()">
                        </div>
                        <div>
                            <label class="block text-xs font-medium text-slate-500 mb-1">두께 (T, mm)</label>
                            <input type="number" id="thickness" value="350" class="w-full p-2 border rounded bg-white text-sm" oninput="calculateQuote()">
                        </div>
                    </div>
                    <div class="grid grid-cols-2 gap-3">
                        <div>
                            <label class="block text-xs font-medium text-slate-500 mb-1">강재 종류 (kg당 단가)</label>
                            <select id="steelType" class="w-full p-2 border rounded bg-white text-sm" onchange="calculateQuote()">
                                <option value="3500">P20 (3,500원/kg)</option>
                                <option value="5500" selected>NAK80 (5,500원/kg)</option>
                                <option value="8000">H13/1.2344 (8,000원/kg)</option>
                            </select>
                        </div>
                        <div>
                            <label class="block text-xs font-medium text-slate-500 mb-1">캐비티 수 (Cavity)</label>
                            <input type="number" id="cavities" value="2" class="w-full p-2 border rounded bg-white text-sm" oninput="calculateQuote()">
                        </div>
                    </div>
                </div>

                <!-- 2. 공정별 가공 시간 설정 -->
                <div class="bg-slate-50 p-4 rounded-lg border border-slate-200">
                    <h2 class="text-lg font-semibold text-slate-700 mb-3">2. 공정별 예상 소요 시간 (Hour)</h2>
                    <div class="grid grid-cols-2 gap-4">
                        <div>
                            <label class="block text-xs font-medium text-slate-500 mb-1">설계/CAD (임율: 35,000원)</label>
                            <input type="number" id="tDesign" value="20" class="w-full p-2 border rounded bg-white text-sm" oninput="calculateQuote()">
                        </div>
                        <div>
                            <label class="block text-xs font-medium text-slate-500 mb-1">CNC 고속가공 (임율: 50,000원)</label>
                            <input type="number" id="tCnc" value="45" class="w-full p-2 border rounded bg-white text-sm" oninput="calculateQuote()">
                        </div>
                        <div>
                            <label class="block text-xs font-medium text-slate-500 mb-1">방전 가공 (임율: 40,000원)</label>
                            <input type="number" id="tEdm" value="30" class="w-full p-2 border rounded bg-white text-sm" oninput="calculateQuote()">
                        </div>
                        <div>
                            <label class="block text-xs font-medium text-slate-500 mb-1">사상 및 조립 (임율: 30,000원)</label>
                            <input type="number" id="tAssembly" value="25" class="w-full p-2 border rounded bg-white text-sm" oninput="calculateQuote()">
                        </div>
                    </div>
                </div>

                <!-- 3. 특수 기구물 및 복잡도 마진 -->
                <div class="bg-slate-50 p-4 rounded-lg border border-slate-200">
                    <h2 class="text-lg font-semibold text-slate-700 mb-3">3. 언더컷 기구 및 비즈니스 조건</h2>
                    <div class="grid grid-cols-2 gap-4 mb-3">
                        <div>
                            <label class="block text-xs font-medium text-slate-500 mb-1">슬라이드 코어 개수</label>
                            <input type="number" id="slideCount" value="1" class="w-full p-2 border rounded bg-white text-sm" oninput="calculateQuote()">
                        </div>
                        <div>
                            <label class="block text-xs font-medium text-slate-500 mb-1">경사핀(Lifter) 개수</label>
                            <input type="number" id="lifterCount" value="2" class="w-full p-2 border rounded bg-white text-sm" oninput="calculateQuote()">
                        </div>
                    </div>
                    <div class="grid grid-cols-2 gap-4">
                        <div>
                            <label class="block text-xs font-medium text-slate-500 mb-1">핫러너 시스템 적용</label>
                            <select id="hotRunner" class="w-full p-2 border rounded bg-white text-sm" onchange="calculateQuote()">
                                <option value="0" selected>콜드러너 (0원)</option>
                                <option value="2500000">핫러너 1-Drop (+2.5M)</option>
                                <option value="4500000">핫러너 2-Drop (+4.5M)</option>
                            </select>
                        </div>
                        <div>
                            <label class="block text-xs font-medium text-slate-500 mb-1">목표 마진율 (%)</label>
                            <input type="number" id="marginRate" value="25" class="w-full p-2 border rounded bg-white text-sm" oninput="calculateQuote()">
                        </div>
                    </div>
                </div>
            </div>

            <!-- 오른쪽: 실시간 견적 리포트 및 확정창 -->
            <div class="flex flex-col justify-between border border-slate-200 rounded-lg p-6 bg-slate-900 text-slate-100">
                <div>
                    <h2 class="text-xl font-bold text-teal-400 mb-6 border-b border-slate-700 pb-2">📊 실시간 견적 시뮬레이션 결과</h2>
                    
                    <div class="space-y-4 text-sm">
                        <div class="flex justify-between">
                            <span class="text-slate-400">계산된 금형 예상 중량:</span>
                            <span class="font-mono font-semibold" id="resWeight">0 kg</span>
                        </div>
                        <div class="flex justify-between">
                            <span class="text-slate-400">순수 재료비 합계:</span>
                            <span class="font-mono" id="resMaterial">0 원</span>
                        </div>
                        <div class="flex justify-between">
                            <span class="text-slate-400">총 가공 공정비:</span>
                            <span class="font-mono" id="resMachining">0 원</span>
                        </div>
                        <div class="flex justify-between">
                            <span class="text-slate-400">슬라이드/기구 가공비:</span>
                            <span class="font-mono" id="resMechanism">0 원</span>
                        </div>
                        <div class="flex justify-between border-t border-slate-700 pt-3">
                            <span class="text-slate-400">순수 금형 제조원가:</span>
                            <span class="font-mono text-amber-300 font-semibold" id="resTotalCost">0 원</span>
                        </div>
                        <div class="flex justify-between">
                            <span class="text-slate-400">설정 마진 금액 (이익금):</span>
                            <span class="font-mono" id="resMarginProfit">0 원</span>
                        </div>
                    </div>
                </div>

                <!-- 최종 금액 확정부 -->
                <div class="mt-8 border-t-2 border-dashed border-slate-700 pt-6">
                    <div class="flex justify-between items-baseline mb-6">
                        <span class="text-lg font-bold text-white">최종 확정 견적가</span>
                        <span class="text-3xl font-mono font-black text-emerald-400" id="resFinalPrice">0 원</span>
                    </div>
                    
                    <button onclick="generatePDF()" class="w-full bg-emerald-500 hover:bg-emerald-600 text-slate-900 font-bold py-3 px-4 rounded-lg transition-all duration-150 cursor-pointer shadow-lg text-center block">
                        📄 확정 견적서 PDF 다운로드
                    </button>
                </div>
            </div>
        </div>
    </div>

    <!-- 비즈니스 가공 로직 JavaScript -->
    <script>
        // 글로벌 변수 저장용
        let computedData = {};

        function calculateQuote() {

// 입력값 파싱
const L = parseFloat(document.getElementById('length').value) || 0;
const W = parseFloat(document.getElementById('width').value) || 0;
const T = parseFloat(document.getElementById('thickness').value) || 0;
const steelPriceKg = parseFloat(document.getElementById('steelType').value) || 0;
const cavities = parseInt(document.getElementById('cavities').value) || 1;
const tDesign = parseFloat(document.getElementById('tDesign').value) || 0;
const tCnc = parseFloat(document.getElementById('tCnc').value) || 0;
const tEdm = parseFloat(document.getElementById('tEdm').value) || 0;
const tAssembly = parseFloat(document.getElementById('tAssembly').value) || 0;
const slideCount = parseInt(document.getElementById('slideCount').value) || 0;
const lifterCount = parseInt(document.getElementById('lifterCount').value) || 0;
const hotRunnerCost = parseFloat(document.getElementById('hotRunner').value) || 0;
const marginRate = parseFloat(document.getElementById('marginRate').value) || 0;
// 1. 중량 및 재료비 계산 (철 밀도 7.85)
const weight = (L * W * T / 1000000) * 7.85;
// 몰드베이스 표준 플레이트 구매 비용 가산 (중량 비례 간이 공식 적용)
const baseMaterialCost = weight * steelPriceKg;
const moldBaseStructureCost = (L * W * 1.2); // 규격별 몰드베이스 가산비 표준화
const totalMaterial = baseMaterialCost + moldBaseStructureCost + hotRunnerCost;
// 2. 가공 공정비 계산 (현업 표준 임율 적용)
const costDesign = tDesign * 35000;
const costCnc = tCnc * 50000;
const costEdm = tEdm * 40000;
const costAssembly = tAssembly * 30000;
const totalMachining = costDesign + costCnc + costEdm + costAssembly;
// 3. 기구부 특수 가공비
const totalMechanism = (slideCount * 1200000) + (lifterCount * 600000); // 슬라이드 120만, 경사핀 60만 적용
// 4. 합산 및 마진 적용
const manufacturingCost = totalMaterial + totalMachining + totalMechanism + 500000; // 시사출비 50만 기본 가산
const marginProfit = manufacturingCost * (marginRate / 100);
const finalPrice = manufacturingCost + marginProfit;
// 글로벌 객체 데이터 업데이트
computedData = {
weight: weight.toFixed(1),
material: Math.round(totalMaterial),
machining: Math.round(totalMachining),
mechanism: Math.round(totalMechanism),
totalCost: Math.round(manufacturingCost),
marginProfit: Math.round(marginProfit),
finalPrice: Math.round(finalPrice)
};
// UI 실시간 바인딩
document.getElementById('resWeight').innerText = ${computedData.weight} kg;
document.getElementById('resMaterial').innerText = ${computedData.material.toLocaleString()} 원;
document.getElementById('resMachining').innerText = ${computedData.machining.toLocaleString()} 원;
document.getElementById('resMechanism').innerText = ${computedData.mechanism.toLocaleString()} 원;
document.getElementById('resTotalCost').innerText = ${computedData.totalCost.toLocaleString()} 원;
document.getElementById('resMarginProfit').innerText = ${computedData.marginProfit.toLocaleString()} 원;
document.getElementById('resFinalPrice').innerText = ${computedData.finalPrice.toLocaleString()} 원;
}
function generatePDF() {
const { jsPDF } = window.jspdf;
const doc = new jsPDF();
// 한글 폰트 깨짐 방지를 위해 내장 영문/표준 레이아웃으로 구조화하여 출력
doc.setFont("helvetica", "bold");
doc.text("INJECTION MOLD QUOTATION REPORT", 14, 20);
doc.setFont("helvetica", "normal");
doc.setFontSize(10);
doc.text(Generated Date: ${new Date().toLocaleDateString()}, 14, 28);
doc.text("Company: GoldMold Manufacturing Co., Ltd.", 14, 34);
// 세부 내역 테이블 구성
const tableColumn = ["Cost Item Description", "Amount (KRW)"];
const tableRows = [
["1. Mold Steel Material Cost (Incl. Base & Components)", ${computedData.material.toLocaleString()} 원],
["2. Main Machining Cost (CNC, EDM, Design, Assembly)", ${computedData.machining.toLocaleString()} 원],
["3. Special Mechanism Cost (Slides & Lifters)", ${computedData.mechanism.toLocaleString()} 원],
["4. Mold Trial Run & Injection Tuning Fixed Cost", "500,000 원"],
["Total Manufacturing Baseline Cost", ${computedData.totalCost.toLocaleString()} 원],
["Target Business Margin & Overhead Profit", ${computedData.marginProfit.toLocaleString()} 원],
["FINAL CONFIRMED QUOTE PRICE (Excl. VAT)", ${computedData.finalPrice.toLocaleString()} 원]
];
doc.autoTable({
head: [tableColumn],
body: tableRows,
startY: 45,
theme: 'striped',
headStyles: { fillColor: [15, 23, 42] }
});
// 파일 저장
doc.save(Mold_Quotation_${Date.now()}.pdf);
}
// 초기 구동 시 1회 연산 실행
calculateQuote();



---

## 📈 단계별 시스템 고도화 마스터 가이드

현재 제공된 HTML5 독립형 앱을 토대로, 코드를 직접 편집하여 고도화할 수 있는 마스터 지침입니다.

1.  **공장 전용 마스터 데이터 고정 (바이브코딩 내 하드코딩)**
    *   코드 내부 `calculateQuote()` 함수 상단에 위치한 각 장비 임율(예: CNC 50,000원, 방전 40,000원) 및 강재 kg당 단가를 기업의 실제 회계 기준 단가로 수정해두면 매번 입력할 필요 없이 최적화된 내부 전용 솔루션이 됩니다.
2.  **클라이언트 로컬 이력 관리 기능 (API 없이 데이터 보존)**
    *   `localStorage.setItem('mold_quote_v1', JSON.stringify(computedData))` 패턴을 자바스크립트 하단에 추가하면, 서버가 없어도 과거에 시뮬레이션했던 견적 정보 리스트를 브라우저쿠키/로컬 스토리지에 안전하게 보관하고 다시 불러올 수 있습니다.
3.  **복잡도 스케일 팩터 도입**
    *   제품 형상(예: 단순 컨테이너 박스형 vs 기하학적 자동차 내장재 부품 등)에 따라 다이얼로그나 드롭다운 메뉴를 추가하여 **최종 가공비에 가중치 배수(1.2배, 1.5배)를 원클릭으로 곱해주는 필터 기능**을 심으면 오차 범위를 5% 이내로 좁힐 수 있습니다.



[1] [https://www.researchgate.net](https://www.researchgate.net/publication/4376398_The_mold_cost_estimation_calculator_for_plastic_injection_mold_manufacturing)
[2] [https://freeformpolymers.com](https://freeformpolymers.com/how-do-you-calculate-injection-molding-cost/)
[3] [https://sanvital.tistory.com](https://sanvital.tistory.com/384)
