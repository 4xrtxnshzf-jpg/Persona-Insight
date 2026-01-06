# Persona-Insight
社会で見せている「表の顔」と、心の奥底に眠る「真の欲求」。16タイプだけでは語れない、あなたの性格のグラデーションを解き明かします。まだ誰も言葉にできていない、本当のあなたを見つける旅へ。
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>超精密 MBTI 2.0 診断 - 64サブタイプ解析</title>
    <style>
        body { font-family: 'Helvetica Neue', Arial, sans-serif; background-color: #f0f2f5; color: #333; line-height: 1.6; padding: 20px; }
        .container { max-width: 700px; margin: 0 auto; background: white; padding: 30px; border-radius: 15px; box-shadow: 0 4px 15px rgba(0,0,0,0.1); }
        h1 { text-align: center; color: #4a90e2; font-size: 1.5rem; }
        .progress { height: 10px; background: #eee; border-radius: 5px; margin: 20px 0; overflow: hidden; }
        .progress-bar { height: 100%; background: #4a90e2; width: 0%; transition: width 0.3s; }
        .question { display: none; }
        .question.active { display: block; }
        .options { display: flex; flex-direction: column; gap: 10px; margin-top: 20px; }
        button { padding: 12px; border: 1px solid #ddd; border-radius: 8px; background: white; cursor: pointer; transition: 0.2s; font-size: 1rem; }
        button:hover { background: #f0f7ff; border-color: #4a90e2; }
        #result { display: none; text-align: center; }
        .type-badge { font-size: 2rem; font-weight: bold; color: #4a90e2; margin: 10px 0; }
        .sub-type { font-size: 1.2rem; color: #e67e22; font-weight: bold; }
        .description { text-align: left; background: #f9f9f9; padding: 15px; border-radius: 8px; margin-top: 20px; }
    </style>
</head>
<body>

<div class="container">
    <h1>超精密性格カルテ MBTI 2.0</h1>
    <div class="progress"><div class="progress-bar" id="progressBar"></div></div>

    <div id="quiz">
        </div>

    <div id="result">
        <h2>あなたの診断結果</h2>
        <div class="type-badge" id="mbtiResult"></div>
        <div class="sub-type" id="subTypeResult"></div>
        <div class="description" id="descResult"></div>
        <button onclick="location.reload()" style="margin-top: 20px; background: #4a90e2; color: white;">もう一度診断する</button>
    </div>
</div>

<script>
const questions = [
    // --- MBTI 4軸 (E/I, S/N, T/F, J/P) ---
    { q: "休日は外で誰かと会う方が元気になる？", trait: "E", val: 1 },
    { q: "自分の考えを話す前に、じっくり頭の中で整理する方だ？", trait: "E", val: -1 },
    { q: "現実的な事実よりも、背後にある意味や可能性にワクワクする？", trait: "N", val: 1 },
    { q: "具体的なデータや実績を何よりも信頼する？", trait: "N", val: -1 },
    { q: "決断を下すとき、論理的な正しさよりも人の感情を優先する？", trait: "F", val: 1 },
    { q: "客観的な分析に基づいて、効率的に物事を進めるのが得意だ？", trait: "F", val: -1 },
    { q: "予定はきっちり決めたい。締め切りは絶対守る？", trait: "J", val: 1 },
    { q: "状況に合わせて柔軟に動きたい。締め切り間際に集中力が出る？", trait: "J", val: -1 },
    
    // --- ダリオ・ナルディ サブタイプ (支配, 創造, 規範, 調和) ---
    { q: "物事を進める時、何よりも「結果」と「スピード」を重視する？", sub: "Dominant" },
    { q: "既存のやり方を壊して、全く新しいアイデアを試すのが好き？", sub: "Creative" },
    { q: "ルールや伝統を守り、正確に物事を進めることに安心する？", sub: "Normalizing" },
    { q: "周囲との摩擦を避け、みんなが納得できる空気感を作りたい？", sub: "Harmonizing" },

    // --- 追加テーマ（承認・防御） ---
    { q: "SNSの反応や他人からの評価がないと、不安を感じやすい？", extra: "External" },
    { q: "ストレスがたまると、自分を責めて内にこもってしまう？", extra: "InternalStress" }
];

let currentIdx = 0;
let scores = { E:0, N:0, F:0, J:0, Dominant:0, Creative:0, Normalizing:0, Harmonizing:0, External:0, InternalStress:0 };

function setupQuiz() {
    const quizDiv = document.getElementById('quiz');
    questions.forEach((q, i) => {
        const div = document.createElement('div');
        div.className = `question ${i === 0 ? 'active' : ''}`;
        div.innerHTML = `<h3>Q${i+1}. ${q.q}</h3>
            <div class="options">
                <button onclick="answer(2)">非常にそう思う</button>
                <button onclick="answer(1)">まあまあそう思う</button>
                <button onclick="answer(-1)">あまり思わない</button>
                <button onclick="answer(-2)">全く思わない</button>
            </div>`;
        quizDiv.appendChild(div);
    });
}

function answer(weight) {
    const q = questions[currentIdx];
    if(q.trait) scores[q.trait] += (weight * q.val);
    if(q.sub) if(weight > 0) scores[q.sub] += weight;
    if(q.extra) if(weight > 0) scores[q.extra] += weight;

    const currentQ = document.querySelectorAll('.question')[currentIdx];
    currentQ.classList.remove('active');
    currentIdx++;

    if(currentIdx < questions.length) {
        const nextQ = document.querySelectorAll('.question')[currentIdx];
        nextQ.classList.add('active');
        document.getElementById('progressBar').style.width = `${(currentIdx / questions.length) * 100}%`;
    } else {
        showResult();
    }
}

function showResult() {
    document.getElementById('quiz').style.display = 'none';
    document.getElementById('progressBar').style.width = '100%';
    document.getElementById('result').style.display = 'block';

    const mbti = (scores.E > 0 ? 'E' : 'I') + (scores.N > 0 ? 'N' : 'S') + (scores.F > 0 ? 'F' : 'T') + (scores.J > 0 ? 'J' : 'P');
    
    const subs = ['Dominant', 'Creative', 'Normalizing', 'Harmonizing'];
    const subType = subs.reduce((a, b) => scores[a] > scores[b] ? a : b);

    const subNames = {
        Dominant: "支配型（成果追求モード）",
        Creative: "創造型（変革探求モード）",
        Normalizing: "規範型（秩序維持モード）",
        Harmonizing: "調和型（共感接続モード）"
    };

    document.getElementById('mbtiResult').innerText = mbti;
    document.getElementById('subTypeResult').innerText = `サブタイプ：${subNames[subType]}`;
    
    let desc = `あなたは${mbti}の資質を持ちながら、特に「${subNames[subType]}」の脳の使い方が強いタイプです。`;
    if(scores.External > 1) desc += " 他者からの承認がエネルギー源になりやすい傾向があります。";
    if(scores.InternalStress > 1) desc += " 疲れが溜まると自分を責めやすいので注意が必要です。";
    
    document.getElementById('descResult').innerText = desc;
}

setupQuiz();
</script>

</body>
</html>
