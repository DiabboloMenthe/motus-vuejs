<template>
  <section class="motusApp">
  <div class="erreur">{{ erreur }}</div>
    <header>
      <input type="text" v-model="proposition" @keyup.enter="checkMot" maxlength="8">
    </header>
    <div>
      <ul>
        <li class="mot" v-for="mot in historique">
          <span class="lettre" v-for="n in 8" :class="{orange: mot.verif[n-1], jaune: mot.existe[n-1]}"> 
            {{ mot.lettres[n-1] }} 
          </span>
        </li>
      </ul>
    </div>  
    <div class="info">{{ info }}</div>   
  </section>
</template>

<script>
  export default {
    data () {
      return {
        listeMot: ['DOUZIEME'.split(''), 'ATTERRIR'.split(''), 'VACANCES'.split(''), 'SOUPIERE'.split(''), 'RONFLEUR'.split(''), 'CERFEUIL'.split(''), 'SEXUELLE'.split(''), 'ENCADRER'.split('')],
        positionInit: [true, false, false, false, false, false, false, false],
        historique: [],
        proposition: '',
        motATrouver: '',
        erreur: '',
        info: ''
      }
    },
    methods: {
      addMot (valeur, verification, existance) {
        this.historique.push({
          lettres: valeur.split(''),
          verif: verification,
          existe: existance
        })
        this.proposition = ''
      },
      checkMot () {
        this.erreur = ''
        if (this.historique.length === 7) {
          // Max de tentatives atteint
          this.erreur = ' Perdu !'
          this.proposition = ''
        } else if (this.proposition.length !== 8) {
          // Mot proposé par assez long
          this.addMot(this.motATrouver[0], this.positionInit, this.fillArray(false, 8))
          this.erreur = 'Le mot doit contenir 8 lettres'
        } else {
          var tempo1 = this.motATrouver
          var tempo2 = this.proposition.split('')
          var verif = []
          var existe = []
          for (var i = 0, c = tempo2.length; i < c; i++) {
            if (tempo1[i].toUpperCase() === tempo2[i].toUpperCase()) {
              // La lettre est bien placée
              verif.push(true)
              existe.push(false)
            } else if (tempo1.filter(l => l.toUpperCase() === tempo2[i].toUpperCase()).length > 0) {
              // La lettre est mal placée
              verif.push(false)
              existe.push(true)
            } else {
              // La lettre n'existe pas
              verif.push(false)
              existe.push(false)
            }
          }
          // Si toutes les lettres sont bien placées
          if (verif.filter(v => v).length === 8) {
            this.info = 'Bravo !'
          }
          this.addMot(this.proposition, verif, existe)
        }
      },
      fillArray (value, len) {
        var arr = []
        for (var i = 0; i < len; i++) {
          arr.push(value)
        }
        return arr
      }
    },
    created () {
      var rand = Math.floor(Math.random() * 8)
      this.motATrouver = this.listeMot[rand]
      this.historique.push({
        lettres: this.motATrouver[0],
        verif: this.positionInit,
        existe: this.fillArray(false, 8)
      })
    }
  }
</script>

<style src="./motus.css"/>
