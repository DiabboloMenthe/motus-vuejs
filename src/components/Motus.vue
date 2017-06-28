<template>
  <section class="motusApp">
  <div class="erreur">{{ erreur }}</div>
    <header>
      <input type="text" v-model="proposition" @keyup.enter="checkMot" maxlength="7">
    </header>
    <div>
      <ul>
        <li class="mot" v-for="mot in historique">
          <span class="lettre" v-for="n in longueurMot" :class="{orange: mot.verif[n-1], jaune: mot.existe[n-1]}"> 
            {{ mot.lettres[n-1] }} 
          </span>
        </li>
      </ul>
    </div>  
    <div :class="{'info-victoire': partieTerminee, info: !partieTerminee}">{{ info }}</div> 
    <br/>
    <button class="reload" onclick="location.reload();">Nouveau mot</button>  
  </section>
</template>

<script>
  import mot7 from '../assets/liste_7.json'

  export default {
    data () {
      return {
        listeMot: mot7,
        historique: [],
        proposition: '',
        motATrouver: '',
        erreur: '',
        info: '',
        tentative: 8,
        longueurMot: 7,
        motDispo: 3360,
        partieTerminee: false
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
        if (this.partieTerminee) {
          event.preventDefault()
          return false
        } else {
          this.erreur = ''
          if (this.proposition.length !== this.longueurMot) {
            // Mot proposé par assez long
            this.addMot(this.motATrouver[0], this.fillArray(false, this.longueurMot, true), this.fillArray(false, this.longueurMot))
            this.erreur = 'Le mot doit contenir ' + this.longueurMot + ' lettres'
          } else {
            var tempo1 = this.motATrouver.split('')
            var tempo2 = this.proposition.split('')
            var verif = []
            var existe = []
            for (var i = 0, c = this.longueurMot; i < c; i++) {
              if (tempo1[i].toUpperCase() === tempo2[i].toUpperCase()) {
                // La lettre est bien placée
                verif.push(true)
                existe.push(false)
              } else if (tempo1.filter(val => val.toUpperCase() === tempo2[i].toUpperCase()).length > 0) {
                // La lettre est mal placée
                verif.push(false)
                existe.push(true)
              } else {
                // La lettre n'existe pas
                verif.push(false)
                existe.push(false)
              }
            }
            this.verifAvancementPartie(verif)
            this.addMot(this.proposition, verif, existe)
          }
        }
      },
      verifAvancementPartie (verif) {
        // Si toutes les lettres sont bien placées
        if (verif.filter(v => v).length === this.longueurMot) {
          this.info = 'Bravo !'
          this.partieTerminee = true
          this.erreur = ''
        } else if (this.historique.length === this.tentative) {
          // Max de tentatives atteint
          this.erreur = ' Perdu :( le mot était : ' + this.motATrouver.toUpperCase()
          this.partieTerminee = true
          this.info = ''
        } else {
          var calcul = this.tentative - this.historique.length
          if (calcul === 1) {
            this.info = calcul + ' tentative restante'
          } else {
            this.info = calcul + ' tentatives restantes'
          }
        }
      },
      fillArray (value, len, firstValue) {
        var arr = []
        var start = 0
        if (firstValue !== null) {
          arr.push(firstValue)
          start = 1
        }
        for (var i = start; i < len; i++) {
          arr.push(value)
        }
        return arr
      }
    },
    created () {
      var rand = Math.floor(Math.random() * this.motDispo)
      this.motATrouver = this.listeMot[rand].mot
      this.historique.push({
        lettres: this.motATrouver.split('')[0],
        verif: this.fillArray(false, this.longueurMot, true),
        existe: this.fillArray(false, this.longueurMot)
      })
      this.info = this.tentative + ' tentatives restantes'
    }
  }
</script>

<style src="./motus.css"/>
