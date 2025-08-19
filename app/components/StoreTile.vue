<template>
  <div @click="navigateTo(store.websiteURL, { external: true })" class="store-tile">
    <div class="text-lg font-semibold">{{ store.name }}</div>
    <div class="text-base mt-2">{{ store.location.address.street }} {{ store.location.address.houseNumber }}
    </div>
    <!-- Open/Closed status -->
    <template v-if="getStoreStatus(store).isOpen">
      <div class="flex items-center mt-3">
        <div class="store-tile__status -open text-base rounded-2xl p-1 px-3">Open</div>
        <span v-if="getStoreStatus(store).nextClosing" class="text-base ml-2">
          closes at {{ getStoreStatus(store).nextClosing }}
        </span>
      </div>
    </template>
    <template v-else>
      <div class="flex items-center mt-3">
        <div class="store-tile__status -closed text-base rounded-2xl p-1 px-3">
          Closed</div>
        <span v-if="getStoreStatus(store).nextOpening" class="text-base ml-2">
          opens {{ getStoreStatus(store).nextOpening }}
        </span>
      </div>
    </template>
  </div>
</template>

<script setup>
const props = defineProps({
  store: Object
})

function getStoreStatus(store) {
  if (!store || !store.openingHours) {
    return { isOpen: false, nextOpening: null, nextClosing: null }
  }

  const now = new Date()
  const currentMinutes = now.getHours() * 60 + now.getMinutes()
  const dayIndex = now.getDay() // Sunday = 0, Monday = 1, ...
  const daysOfWeek = ["sunday", "monday", "tuesday", "wednesday", "thursday", "friday", "saturday"]

  const today = daysOfWeek[dayIndex]
  const hours = store.openingHours[today]

  // Helper to parse "08:00+01:00" → minutes since midnight
  const parseTime = (timeStr) => {
    if (!timeStr) return null
    const [hour, minute] = timeStr.slice(0, 5).split(":").map(Number)
    return hour * 60 + minute
  }

  if (hours && hours.opensAt && hours.closesAt) {
    const openMinutes = parseTime(hours.opensAt)
    const closeMinutes = parseTime(hours.closesAt)
    const closeTime = hours.closesAt.slice(0, 5)

    if (openMinutes !== null && closeMinutes !== null) {
      // Handle overnight (e.g. 20:00 → 02:00)
      if (closeMinutes < openMinutes) {
        if (currentMinutes >= openMinutes || currentMinutes < closeMinutes) {
          return { isOpen: true, nextOpening: null, nextClosing: `${closeTime}` }
        }
      } else {
        if (currentMinutes >= openMinutes && currentMinutes < closeMinutes) {
          return { isOpen: true, nextOpening: null, nextClosing: `${closeTime}` }
        }
      }
    }
  }

  // Not open now → find next opening
  for (let i = 0; i < 7; i++) {
    const checkDayIndex = (dayIndex + i) % 7
    const checkDay = daysOfWeek[checkDayIndex]
    const dayHours = store.openingHours[checkDay]

    if (dayHours?.opensAt) {
      const openTime = dayHours.opensAt.slice(0, 5)
      if (i === 0 && currentMinutes < parseTime(dayHours.opensAt)) {
        return { isOpen: false, nextOpening: `today at ${openTime}`, nextClosing: null }
      } else if (i === 1) {
        return { isOpen: false, nextOpening: `tomorrow at ${openTime}`, nextClosing: null }
      } else if (i > 1) {
        return { isOpen: false, nextOpening: `${checkDay} at ${openTime}`, nextClosing: null }
      }
    }
  }

  return { isOpen: false, nextOpening: null, nextClosing: null }
}
</script>

<style lang="scss" scoped>
.store-tile {
  padding: 1.5rem;
  border: 1px solid #ddd;
  border-radius: .5rem;
  cursor: pointer;
  margin-bottom: 1rem;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
  transition: box-shadow 0.2s ease, transform 0.2s ease;

  &:hover {
    border-color: black;
    box-shadow: 0 6px 12px rgba(0, 0, 0, 0.15);
  }

  &__status {
    &.-open {
      background: green;
      color: white;
    }

    &.-closed {
      background: #EEE;
      color: black;
    }
  }
}
</style>