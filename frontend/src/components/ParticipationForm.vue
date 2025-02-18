<template>
  <div class="container">
    <h2>Enregistrer une participation</h2>
    <!-- Un formulaire pour saisir les valeurs d'une participation à ajouter -->
    <form @submit.prevent="ajouteParticapation">
      <div>
        <div>Personne :</div>

        <select v-model="selectedPers">
          <option v-for="personne in data.personnes" :key="personne.id" :value="personne">
            {{ personne.nom }} {{ personne.prenom }}
          </option>
        </select>

        <div>Projet :</div>

        <select v-model="selectedProjet">
          <option v-for="projet in data.projets" :key="projet.id" :value="projet.nom">
            {{ projet.nom }}
          </option>
        </select>

        <div>Rôle :</div>

        <input id="role" v-model="inputRole" size="35" >

        <div>Pourcentage :</div>
        <input id="pourcentage" type="range" v-model="selectedPourcentage" min="0" max="100" step="1">

        <!-- Affichage du pourcentage -->
        <p>Valeur sélectionnée : <strong>{{ selectedPourcentage }}%</strong></p>

        <br>

        <button type="submit">Enregistrer</button>
      </div>
    </form>
  </div>
</template>

<script setup>
import {onMounted, reactive, ref} from "vue";
// Importer la fonction doAjaxRequest qui gère les erreurs d'API
import doAjaxRequest from "@/util/util.js"

// Pour réinitialiser le formulaire
const participationVide = {
  role: "",
  pourcentage: "",
  projet_id: "",
  personne_matricule: "",
};

// Les données du composant
let data = reactive({
  formulaire: {...participationVide},
  projets: [],
  personnes: [],
  roles: [],
});

let selectedProjet = ref(null);
let selectedPers = ref(null);
let selectedPourcentage = ref(0);
let inputRole = ref(null);

function ajouteParticapation() {
  // Mise à jour des valeurs du formulaire avant l'envoi
  data.formulaire = {
    role: inputRole.value,
    pourcentage: Number(selectedPourcentage.value/100),
    projetNom: selectedProjet.value,
    personneNom: selectedPers.value.nom,
    personnePrenom: selectedPers.value.prenom // vous devrez aussi ajouter ceci
  };
console.log( data.formulaire)
  // Ajoutez ces logs
  console.log("Type de personne_matricule:", typeof data.formulaire.personne_matricule);
  console.log("Valeur complète avant envoi:", data.formulaire);
  const options = {
    method: "POST",
    body: JSON.stringify(data.formulaire),
    headers: {
      "Content-Type": "application/json",
    },
  };

  doAjaxRequest("/api/participations", options)
    .then((result) => {
      console.log("Participation ajoutée :", result);
      alert("Participation enregistrée avec succès !");

      // Réinitialisation du formulaire
      data.formulaire = { ...participationVide };
      selectedProjet.value = null;
      selectedPers.value = null;
      selectedPourcentage.value = 0;
    })
    .catch(error => alert(error.message));
}


function fetchProjet() {
  doAjaxRequest("/api/projets") // Méthode GET par défaut
    .then((result) => {
      data.projets = result._embedded.projets;
    })
    .catch(error => alert(error.message));
}

function fetchPersonne() {
  doAjaxRequest("/api/personnes") // Méthode GET par défaut
    .then((result) => {
      data.personnes = result._embedded.personnes;
      console.log("Personnes récupérées :", result);
    })
    .catch(error => alert(error.message));
}

// Appeler la fonction fetchProjet() pour récupérer la liste des projets au chargement du composant
onMounted(() => {
  fetchProjet();
  fetchPersonne();
});
</script>

<!-- Un CSS pour ce composant -->
<style scoped>
.container {
  margin: 2rem auto;
  max-width: 600px;
  background-color: #f9f9f9;
  padding: 20px;
  border-radius: 10px;
  box-shadow: 0px 4px 10px rgba(0, 0, 0, 0.1);
}

.form-group {
  margin-bottom: 15px;
}

.label {
  display: block;
  font-weight: bold;
  margin-bottom: 5px;
}

.input-field {
  width: 100%;
  padding: 10px;
  font-size: 1rem;
  border: 1px solid #ccc;
  border-radius: 5px;
}

.input-field:focus {
  outline: none;
  border-color: #007BFF;
  box-shadow: 0 0 5px rgba(0, 123, 255, 0.5);
}

.button-container {
  text-align: center;
}

.submit-button {
  padding: 10px 20px;
  font-size: 1rem;
  color: #fff;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.submit-button:hover {
  background-color: #0056b3;
}

.table-container {
  margin-top: 20px;
}

table {
  width: 100%;
  border-collapse: collapse; /* Supprime les bordures en double */
  margin-top: 10px;
}

thead th {
  background-color: #007BFF;
  color: #fff;
  padding: 10px;
  text-align: left; /* Alignement à gauche */
  border: 1px solid #ddd;
}

tbody td {
  border: 1px solid #ddd;
  padding: 8px;
  text-align: left; /* Alignement à gauche */
}

tbody tr:nth-child(odd) {
  background-color: #f2f2f2; /* Lignes alternées pour une meilleure lisibilité */
}

tbody tr:hover {
  background-color: #eaeaea;
}

th, td {
  font-size: 0.9rem;
  text-align: left;
}

code {
  color: #d63384;
  font-weight: bold;
  background-color: #f8f9fa;
  padding: 2px 4px;
  border-radius: 3px;
}

</style>
