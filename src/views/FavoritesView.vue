<template>
  <header>
    <h1>Deine Favoriten</h1>
    <h2>Krams den du besonders knorke findest</h2>
    <hr />
  </header>

  <div class="content-container">
    <main>
      <ul>
        <li v-for="(favorite, index) of favorites" :key="favorite.id">
          <div class="card">
            <img
              class="card__img"
              :src="favorite.picture || placeholderPic"
              @error="(event) => (event.target.src = placeholderPic)"
              alt="Bild des Artikels"
            />
            <div class="card__fav" @click="deleteLike(favorite, index)">
              <FavoritesIcon class="card__fav--icon is-favorite" />
            </div>
            <div class="card__details">
              <h3 class="card__headline">{{ favorite.title.toUpperCase() }}</h3>
              <p class="card__location">
                <LocationIcon /> {{ favorite.zipcode + ' ' + favorite.town }}
              </p>
              <ArrowRightIcon class="card__arrow" />
            </div>
          </div>
        </li>
      </ul>
    </main>
  </div>
</template>

<script setup>
import { useDatabaseStore } from '@/stores/database'
import { onBeforeMount, reactive, toRaw } from 'vue'
import LocationIcon from '@/components/icons/IconLocation.vue'
import FavoritesIcon from '@/components/icons/IconFavorites.vue'
import ArrowRightIcon from '@/components/icons/IconArrowRight.vue'
import { useAuthStore } from '@/stores/auth'
import placeholderPic from '@/assets/kk-placeholder-pic.png'

const store = useDatabaseStore()
const auth = useAuthStore()

let favorites = reactive([])

function filterData() {
  store.dataFromApi.forEach((offer) => {
    toRaw(offer.likedBy).forEach((id) => {
      if (id === auth.user.uid) {
        favorites.push(toRaw(offer))
      }
    })
  })
}

function deleteLike(offer, index) {
  store.deleteOfferLikedBy(offer.id, auth.user.uid)
  favorites.splice(index, 1)
}
onBeforeMount(() => {
  store.getOffers()
  filterData()
})
</script>

<style scoped>
@import url('@/assets/main.css');
@import url('@/assets/base.css');

.content-container {
  margin-top: 8rem;
  margin-bottom: 1.5rem;
}

ul {
  margin: 0;
  padding: 0;
}

li {
  list-style: none;
  margin: 25px auto;
}
.card {
  position: relative;
  border-radius: 12px;
  overflow: hidden;
  width: 385px;
  height: 326px;
  margin: 0 auto;
  bottom: 6rem;
  background-color: rgba(26, 26, 26, 0.25);
}

.card__img {
  width: 385px;
  height: 250px;
  object-fit: contain;
  object-position: center;
}

.card__fav {
  position: absolute;
  top: 15px;
  right: 15px;
  border-radius: 50%;
  background-color: white;
  height: 21px;
  width: 21px;
  display: flex;
  justify-content: center;
  align-items: center;
  cursor: pointer;
}

.is-favorite {
  color: var(--cc-orange);
}

.is-no-favorite {
  color: white;
}

.card__fav--icon {
  width: 17.5px;
  height: 15.75px;
}

.card__headline {
  font-size: 16px;
  font-style: normal;
  font-weight: 400;
  line-height: 24px;
  color: white;
}

.card__location {
  font-size: 14px;
  font-style: normal;
  font-weight: 400;
  line-height: 20px;
}

.card__details {
  background-color: black;
  color: white;
  padding: 16px;
}

.card__arrow {
  position: absolute;
  bottom: 20px;
  right: 15px;
}
</style>
