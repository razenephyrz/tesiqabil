<script lang="ts">
  import { page } from "$app/stores";
  import { derived } from "svelte/store";

  const score = derived(page, ($page) => parseInt($page.url.searchParams.get("score") || "0"));
  const correct = derived(page, ($page) => parseInt($page.url.searchParams.get("correct") || "0"));
  const wrong = derived(page, ($page) => parseInt($page.url.searchParams.get("wrong") || "0"));

  function getIQ(score: number) {
    if (score >= 95) return 140;
    if (score >= 85) return 130;
    if (score >= 70) return 120;
    if (score >= 60) return 110;
    if (score >= 45) return 100;
    if (score >= 30) return 90;
    return 80;
  }

  function getGrade(iq: number) {
    if (iq >= 130) return { grade: "A+", color: "bg-green-500 text-white" };
    if (iq >= 120) return { grade: "A", color: "bg-blue-500 text-white" };
    if (iq >= 110) return { grade: "B", color: "bg-cyan-500 text-white" };
    if (iq >= 100) return { grade: "C", color: "bg-yellow-500 text-white" };
    return { grade: "D", color: "bg-red-500 text-white" };
  }

  let currentScore = 0;
  let currentIQ = 80;
  let grade = getGrade(currentIQ);

  $: score.subscribe((val) => {
    currentScore = val;
    currentIQ = getIQ(val);
    grade = getGrade(currentIQ);
  });
</script>

<!-- Result Card Dark Purple Theme -->
<div class="flex justify-center items-center min-h-screen px-4">

  <div class="w-full max-w-md bg-white rounded-3xl shadow-2xl p-8 border-2 border-purple-200 text-center">

    <h1 class="text-4xl font-extrabold mb-2 text-purple-700">🎉 Tes Selesai! 🎉</h1>
    <p class="text-lg text-gray-700 mb-4">Skor akhir kamu:</p>

    <p class="text-6xl font-extrabold text-purple-800 my-2">
      {currentScore}
    </p>

    <div class="mt-4 space-y-2">
      <p class="text-lg">
        ✅ Jawaban benar: 
        <span class="font-bold text-green-600">{$correct}</span>
      </p>
      <p class="text-lg">
        ❌ Jawaban salah: 
        <span class="font-bold text-red-500">{$wrong}</span>
      </p>
    </div>

    <div class="mt-6">
      <p class="text-lg mb-2">Perkiraan IQ kamu:</p>
      <div class="inline-block rounded-xl bg-purple-100 text-purple-800 text-xl font-bold px-6 py-2 shadow">
        {currentIQ}
      </div>
    </div>

    <div class="mt-4">
      <p class="text-lg mb-2">Grade kamu:</p>
      <div class={"inline-block rounded-xl text-xl font-bold px-6 py-2 shadow " + grade.color}>
        {grade.grade}
      </div>
    </div>

    <a href="/" 
      class="mt-8 inline-block bg-purple-700 hover:bg-purple-800 text-white font-bold py-3 px-6 rounded-full shadow-lg transition-transform transform hover:scale-105">
      🔄 Coba Lagi
    </a>

  </div>
</div>
