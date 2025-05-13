<script lang="ts">
  import { questions } from "$lib/questions";
  import { goto } from "$app/navigation";
  import { onMount } from 'svelte';

  let current = 0;
  let score = 0;
  let correct = 0;
  let wrong = 0;

  let timePerQuestion = 0; // waktu berjalan di soal ini (detik)
  let totalTime = 0;        // total waktu semua soal (detik)
  let timer: ReturnType<typeof setInterval>;

  function startTimer() {
    timer = setInterval(() => {
      timePerQuestion++;
      totalTime++;
    }, 1000);
  }

  function selectOption(index: number) {
    clearInterval(timer); // hentikan timer saat memilih

    if (index === questions[current].correctIndex) {
      score += 5;
      correct++;
    } else {
      score -= 1;
      wrong++;
    }

    if (current < questions.length - 1) {
      current++;
      timePerQuestion = 0; // reset stopwatch soal berikutnya
      startTimer();        // mulai lagi
    } else {
      const params = new URLSearchParams({
        score: score.toString(),
        correct: correct.toString(),
        wrong: wrong.toString(),
        time: totalTime.toString()
      });
      goto(`/result?${params}`);
    }
  }

  onMount(() => {
    startTimer(); // mulai timer pertama kali
  });

  // Helper untuk format detik ke "mm:ss"
  function formatTime(seconds: number): string {
    const mins = Math.floor(seconds / 60).toString().padStart(2, '0');
    const secs = (seconds % 60).toString().padStart(2, '0');
    return `${mins}:${secs}`;
  }
</script>

<div class="w-full max-w-md bg-white rounded-3xl shadow-2xl p-6 border-2 border-purple-200">

  <!-- Progress Soal -->
  <div class="mb-4">
    <h1 class="text-2xl font-extrabold text-purple-700">Tes IQ - Soal {current + 1} / {questions.length}</h1>
    <div class="w-full h-4 bg-purple-100 rounded-full overflow-hidden mt-2">
      <div
        class="h-full bg-purple-800 transition-all duration-300"
        style="width: {(current + 1) / questions.length * 100}%"
      ></div>
    </div>
  </div>

  <!-- Timer Stopwatch -->
  <div class="mt-2 grid grid-cols-2 gap-2">
    <div class="p-2 bg-purple-50 rounded-xl shadow text-center">
      <p class="text-sm text-gray-600">Waktu Soal Ini</p>
      <p class="text-xl font-bold text-purple-700">{formatTime(timePerQuestion)}</p>
    </div>
    <div class="p-2 bg-purple-50 rounded-xl shadow text-center">
      <p class="text-sm text-gray-600">Total Waktu</p>
      <p class="text-xl font-bold text-purple-700">{formatTime(totalTime)}</p>
    </div>
  </div>

  <!-- Soal Gambar -->
  <img src={questions[current].image} class="w-full max-w-sm mx-auto rounded-2xl shadow-lg border-2 border-purple-300 mt-4" alt="Soal" />

  <!-- Opsi Pilihan -->
  <div class="grid grid-cols-2 md:grid-cols-3 gap-4 mt-6">
    {#each questions[current].options as option, index}
      <button 
        on:click={() => selectOption(index)} 
        class="btn btn-outline rounded-xl shadow hover:shadow-lg transform hover:scale-105 transition duration-200">
        <img src={option} alt="Option" class="rounded-lg" />
      </button>
    {/each}
  </div>
</div>
