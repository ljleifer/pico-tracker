<template>
  <div class="topbar">
    <div class="left">
    </div>
    <div class="right">
      <img src="../assets/edit.png" @click="editMode = !editMode" title="Edit"/>
      <img src="../assets/save.png" @click="saveData" title="Save"/>
    </div>
  </div>

  <div class="body">
    <div v-if="editMode">
      <div v-if="trackData.length == 0" class="box fixed">
        <p>No Tracks</p>
      </div>
      <div v-else v-for="track, trackIndex in trackData" class="box column fixed">
        <img src="../assets/delete.png" @click="deleteTrack(trackIndex)" title="Delete Track"/>
        <p>Track Name:</p>
        <input type="text" v-model="track.name">
        <p>Track Progress:</p>
        <input type="text" v-model="track.progress">
      </div>
      <img src="../assets/add.png" @click="addNewTrack" title="Add New Track"/>
    </div>

    <div v-else>
      <div v-if="trackData.length == 0" class="box fixed">
        <p>No Tracks</p>
      </div>
      <div v-else v-for="track, trackIndex in trackData" class="box column fixed">
        <p>{{ track.name }}</p>
        <div>
          <div v-for="value, progressIndex in track.progress.split('')">
            <img v-if="value == '.'" src="../assets/circle.png" @click="alterTrackValue(track, progressIndex)"/>
            <img v-if="value == '/'" src="../assets/circle-slash.png" @click="alterTrackValue(track, progressIndex)"/>
            <img v-if="value == 'x'" src="../assets/circle-x.png" @click="alterTrackValue(track, progressIndex)"/>
            <img v-if="value == '|'" src="../assets/line.png" style="cursor: default;"/>
            <img v-if="progressIndex < track.progress.length-1" src="../assets/dash.png" style="cursor: default; margin: 7px 2px;"/>
          </div>
        </div>
      </div>
    </div>
  </div>

  <div class="body" style="margin-top: 10px;">
    <div v-if="editMode">
      <div v-if="characterData.length == 0" class="box fixed">
        <p>No Characters</p>
      </div>
      <div v-else v-for="character, characterIndex in characterData" class="box column fixed">
        <img src="../assets/delete.png" @click="deleteCharacter(characterIndex)" title="Delete Character"/>
        <p>Character Name:</p>
        <input type="text" v-model="character.characterName">
        <p>Player Name:</p>
        <input type="text" v-model="character.playerName">
        <p>Aspects:</p>
        <div v-if="character.aspects.length == 0" class="box">
          <p>NONE</p>
        </div>
        <div v-else v-for="aspect, aspectIndex in character.aspects" class="box column">
          <img src="../assets/delete.png" @click="deleteAspect(characterIndex, aspectIndex)" title="Delete Aspect"/>
          <p>Aspect Name:</p>
          <input type="text" v-model="aspect.name">
          <p>Aspect Health:</p>
          <input type="text" v-model="aspect.health">
        </div>
        <img src="../assets/add.png" @click="addNewAspect(character.aspects)" title="Add New Aspect"/>
      </div>
      <img src="../assets/add.png" @click="addNewCharacter" title="Add New Character"/>
    </div>

    <div v-else>
      <div v-if="characterData.length == 0" class="box fixed">
        <p>No Characters</p>
      </div>
      <div v-else v-for="character in characterData" class="box column fixed">
        <p>{{ character.characterName }}</p>
        <p>{{ character.playerName }}</p>
        <p>Aspects:</p>
        <div v-if="character.aspects.length == 0" class="box">
          <p>NONE</p>
        </div>
        <div v-else v-for="aspect, aspectIndex in character.aspects" class="box column" style="margin: 5px;">
          <p>{{ aspect.name }}</p>
          <div>
            <div v-for="value, healthIndex in aspect.health.split('')" style="margin: 0 2px;">
              <img v-if="value == 'h'" src="../assets/heart-healthy.png" @click="alterHealthValue(character, aspectIndex, healthIndex)"/>
              <img v-if="value == 'd'" src="../assets/heart-damaged.png" @click="alterHealthValue(character, aspectIndex, healthIndex)"/>
              <img v-if="value == 'b'" src="../assets/heart-broken.png" @click="alterHealthValue(character, aspectIndex, healthIndex)"/>
              <img v-if="value == '.'" src="../assets/dot.png" style="cursor: default;"/>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'PicoTracker',
  components: {},
  data() {
    return {
      characterData: [],
      trackData: [],
      editMode: false,
    }
  },
  methods: {
    addNewCharacter() {
      this.characterData.push({
        characterName: '',
        playerName: '',
        aspects: [],
      })
    },
    addNewAspect(aspects) {
      aspects.push({
        name: '',
        health: '',
      })
    },
    alterHealthValue(character, aspectIndex, healthIndex) {
      const health = character.aspects[aspectIndex].health
      let value = health[healthIndex]
      switch (value) {
        case 'h': value = 'd'; break;
        case 'd': value = 'b'; break;
        case 'b': value = 'h'; break;
      }
      character.aspects[aspectIndex].health = health.substring(0,healthIndex) + value + health.substring(healthIndex+1)
    },
    deleteCharacter(characterIndex) {
      this.characterData.splice(characterIndex, 1)
    },
    deleteAspect(characterIndex, aspectIndex) {
      this.characterData[characterIndex].aspects.splice(aspectIndex, 1)
    },
    addNewTrack() {
      this.trackData.push({
        name: '',
        progress: '',
      })
    },
    alterTrackValue(track, progressIndex) {
      const progress = track.progress
      let value = progress[progressIndex]
      switch (value) {
        case '.': value = '/'; break;
        case '/': value = 'x'; break;
        case 'x': value = '.'; break;
      }
      track.progress = progress.substring(0,progressIndex) + value + progress.substring(progressIndex+1)
    },
    deleteTrack(trackIndex) {
      this.trackData.splice(trackIndex, 1)
    },
    saveData() {
      localStorage.setItem('picoCharacterData', JSON.stringify(this.characterData))
      localStorage.setItem('picoTrackData', JSON.stringify(this.trackData))
    },
  },
  async beforeMount() {
    this.characterData = JSON.parse(localStorage.getItem('picoCharacterData')) || [{"characterName":"Guinness the Beetle","playerName":"Logan","aspects":[{"name":"Widewings","health":"hhh"},{"name":"Tankling + Rotund","health":"h.h.hh"},{"name":"Angulomorph","health":"h."},{"name":"Blockomorph","health":"h."}]},{"characterName":"Inwë Ælensar","playerName":"Kristian","aspects":[{"name":"Widewings + Hydrophobic","health":"hhh.hh"},{"name":"Thimblefriend + Exaggero","health":"hhhh"},{"name":"Sewing Needle","health":"hhhh"}]},{"characterName":"Xana Goodfellow","playerName":"Ethan","aspects":[{"name":"Hardshell + Ornate","health":"h.hhh.hh"},{"name":"Mind Palace","health":"hh"},{"name":"Logic Center","health":"hhhh"},{"name":"Eurekaling","health":"hhh"}]},{"characterName":"Shadowsting the Dracomantis","playerName":"Nick","aspects":[{"name":"Mandibles + Cannibalizing + Xenic","health":"hh.hhh.h"},{"name":"Many-Eyes","health":"h.h"},{"name":"Flutterflaps","health":"hh"},{"name":"Goggles","health":"hhhhh"}]}]
    this.trackData = JSON.parse(localStorage.getItem('picoTrackData')) || [{"name":"test","progress":"..|.."},{"name":"also","progress":"..."}]
  },
}
</script>

<style scoped style="scss">
.topbar {
  height: 50px;
  width: 100%;

  .left {
    flex: 1;
  }

  .right > img {
    height: 30px;
    width: 30px;
    margin: 10px;
  }
}
.body {
  margin: 0 20px;
}
.box {
  border: 2px solid black;
  border-radius: 20px;
  margin: 10px;
  padding: 10px;
}
.fixed {
  width: 300px;
}
.column {
  flex-direction: column;
}
img {
  height: 20px;
  width: 20px;
  margin: 7px auto;
}
p {
  margin: 0;
}
</style>
