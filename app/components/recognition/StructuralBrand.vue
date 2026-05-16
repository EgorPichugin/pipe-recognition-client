<template>
  <div class="brand-row">
    <div class="mark" aria-hidden="true">
      <span></span>
      <span></span>
      <span></span>
    </div>
    <p>Structural Field Scanner</p>
  </div>
</template>

<style scoped>
.brand-row {
  display: flex;
  align-items: center;
  gap: 0.85rem;
  color: #aebbc2;
  font-size: 0.82rem;
  font-weight: 700;
  letter-spacing: 0.16em;
  text-transform: uppercase;
}

.brand-row p {
  margin: 0;
}

.mark {
  display: grid;
  grid-template-columns: repeat(3, 0.45rem);
  gap: 0.22rem;
  padding: 0.62rem;
  border: 1px solid rgba(140, 255, 232, 0.22);
  border-radius: 0.5rem;
  background: rgba(7, 15, 18, 0.72);
  box-shadow: inset 0 0 24px rgba(122, 255, 230, 0.08);
}

.mark span {
  width: 0.45rem;
  height: 1.1rem;
  border-radius: 999px;
  background: linear-gradient(#92ffe9, #327d72);
}

.mark span:nth-child(2) {
  height: 1.65rem;
  background: linear-gradient(#ffc973, #d95768);
}
</style>
