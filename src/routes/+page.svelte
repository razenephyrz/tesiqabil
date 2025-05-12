<script lang="ts">
  import { questions } from "$lib/questions";
  import { goto } from "$app/navigation";

  let current = 0;
  let score = 0;
  let correct = 0;
  let wrong = 0;

  function selectOption(index: number) {
    if (index === questions[current].correctIndex) {
      score += 5;
      correct++;
    } else {
      score -= 1;
      wrong++;
    }

    if (current < questions.length - 1) {
      current++;
    } else {
      const params = new URLSearchParams({
        score: score.toString(),
        correct: correct.toString(),
        wrong: wrong.toString()
      });
      goto(`/result?${params}`);
    }
  }
</script>

<div class="w-full max-w-md bg-white rounded-3xl shadow-2xl p-6 border-2 border-purple-200">

  <!-- Progress -->
  <div class="mb-4">
    <h1 class="text-2xl font-extrabold text-purple-700">Tes IQ - Soal {current + 1} / {questions.length}</h1>
    <div class="w-full h-4 bg-purple-100 rounded-full overflow-hidden mt-2">
      <div
        class="h-full bg-purple-800 transition-all duration-300"
        style="width: {(current + 1) / questions.length * 100}%"
      ></div>
    </div>
  </div>

  <!-- Soal Gambar -->
  <img src={questions[current].image} class="w-full max-w-sm mx-auto rounded-2xl shadow-lg border-2 border-purple-300" alt="Soal" />

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
