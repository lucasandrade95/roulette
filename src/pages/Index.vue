<template>
  <q-page class="column bg-white">
    <section class="section justify-center text-center">
<!--      <div class="container" id="app">-->
<!--        <div class="col">-->
<!--          <div class="col">-->
<!--            <div class="text-primary q-mt-sm" style="font-size: 35px">-->
<!--              Roleta de Prêmios! <br/>-->
<!--              <input type="button" class="roulette button q-mt-md" value="Rodar Roleta" id='spin' v-on:click="spin"/>-->
<!--              <div style="width: 18%;margin-left:42%">-->
<!--                <q-input v-model="new_option" v-on:keyup.enter="addOptions" class="col q-mt-xl q-pa-sm" filled square bg-color="white" label="Novo item" dense>-->
<!--                  <template v-slot:append>-->
<!--                    <q-btn round dense flat icon="add" />-->
<!--                  </template>-->
<!--                </q-input>-->
<!--              </div>-->
<!--              <q-btn outline rounded class="button text-primary q-mb-sm" v-on:click="addOptions">Adicionar item</q-btn> <br/>-->
<!--              <q-btn outline rounded class="button text-red" v-on:click="removeOptions(option)">Remover último item</q-btn>-->
<!--              <div class="q-ml-xl flex content-stretch" style="position: relative;width: 100%;">-->
<!--                <canvas id="canvas" width="500" height="500" style="margin-left: -80px; margin-top: -325px;"></canvas>-->
<!--              </div>-->
<!--            </div>-->
<!--          </div>-->
<!--        </div>-->
<!--      </div>-->
    </section>
  </q-page>
</template>

<script>

</script>
<style>
  .button {
    display: inline-flex;
    flex-direction: column;
    align-items: stretch;
    position: relative;
    outline: 0;
    border: 0;
    vertical-align: middle;
    padding: 0;
    font-size: 14px;
    line-height: 1.715em;
    text-decoration: none;
    background: transparent;
    font-weight: 500;
    text-transform: uppercase;
    text-align: center;
    width: auto;
    height: auto;
  }
  .roulette {
    border-radius: 10px;
    color: white;
    background: #1976d2;
    padding: 10px;
    cursor: pointer
  }
</style>
