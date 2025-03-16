<template>
  <!-- Navigation -->
  <div class="app">
    <!-- Add Font Awesome CDN -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    
    <nav>
      <button 
        v-for="section in ['home', 'projects', 'contact']" 
        :key="section"
        @click="navigateToSection(section)"
        :class="{ active: activeSection === section }"
        class="nav-button"
      >
        {{ section.charAt(0).toUpperCase() + section.slice(1) }}
      </button>
    </nav>

    <!-- Home Section -->
    <section v-if="activeSection === 'home'" class="section">
      <h1>Welcome to Anirudh Iyer's Website</h1>
      
      <div class="cookie-section">
        <p v-if="cookieCount === 0" class="cookie-prompt">CLICK THE COOKIE</p>
        <img 
          src="https://assets.caseys.com/m/209fe9deebc694e6/400x400-9843710122_base.PNG" 
          alt="Sugar Cookie with Sprinkles" 
          @click="incrementCookies" 
          class="cookie-image"
        />
        <p class="cookie-count">{{ cookieCount }} cookies clicked</p>
      </div>
      
      <button @click="toggleAboutMe" class="btn">{{ showAboutMe ? 'Hide' : 'Show' }} About Me</button>
      
      <div v-show="showAboutMe" class="about-me">
        <p>Hello! My name is Anirudh Iyer, I am 22 years old and a Computer Science student at Georgia State University. In my free time I like building/repairing keyboards, hanging out with friends, playing drumset, and working on personal CS projects.</p>
      </div>
    </section>

    <!-- Projects Section -->
    <section v-if="activeSection === 'projects'" class="section">
      <h2>My Projects</h2>
      <div class="project-summary">
        <button 
          class="filter-btn"
          :class="{ active: selectedFilter === 'all' }"
          @click="selectedFilter = 'all'"
        >
          Total Projects: {{ projectSummary.total }}
        </button>
        <button 
          class="filter-btn"
          :class="{ active: selectedFilter === 'inProgress' }"
          @click="selectedFilter = selectedFilter === 'inProgress' ? 'all' : 'inProgress'"
        >
          In Progress: {{ projectSummary.inProgress }}
        </button>
        <button 
          class="filter-btn"
          :class="{ active: selectedFilter === 'completed' }"
          @click="selectedFilter = selectedFilter === 'completed' ? 'all' : 'completed'"
        >
          Completed: {{ projectSummary.completed }}
        </button>
      </div>
      <div v-if="filteredProjects.length === 0" class="no-projects">
        No projects available
      </div>
      <div v-else class="projects-list">
        <div v-for="project in filteredProjects" :key="project.id" class="project-card">
          <div class="project-header">
            <h3>{{ project.title }}</h3>
            <span class="project-date">{{ project.date }}</span>
          </div>
          <ul class="project-main-points">
            <li class="project-description">{{ project.description }}</li>
          </ul>
          <ul class="project-details">
            <li v-for="(detail, index) in project.details" :key="index">
              {{ detail }}
            </li>
          </ul>
        </div>
      </div>
    </section>

    <!-- Contact Section -->
    <section v-if="activeSection === 'contact'" class="section">
      <h2>Contact Me</h2>
      <div class="social-links">
        <a href="/resume.pdf" download="Anirudh Iyer Resume.pdf" class="social-btn resume-btn">
          <i class="fas fa-file-pdf"></i>
          Download Resume
        </a>
        <a href="https://github.com/anirudh-90" target="_blank" rel="noopener noreferrer" class="social-btn github-btn">
          <i class="fab fa-github"></i>
          GitHub
        </a>
        <a href="https://www.linkedin.com/in/anirudh-iyer-420806293" target="_blank" rel="noopener noreferrer" class="social-btn linkedin-btn">
          <i class="fab fa-linkedin"></i>
          LinkedIn
        </a>
      </div>
      <form @submit.prevent class="contact-form">
        <div class="form-group">
          <label for="name">Name:</label>
          <input 
            id="name"
            v-model="contactForm.name" 
            type="text" 
            required
          >
        </div>
        
        <div class="form-group">
          <label for="email">Email:</label>
          <input 
            id="email"
            v-model="contactForm.email" 
            type="email" 
            required
          >
        </div>
        
        <div class="form-group">
          <label for="message">Message:</label>
          <textarea 
            id="message"
            v-model="contactForm.message" 
            required
          ></textarea>
        </div>
        
        <button type="submit" class="btn">Send Message</button>
      </form>
    </section>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, watch } from 'vue'

// State management using ref()
const activeSection = ref('home')
const cookieCount = ref(0)
const showAboutMe = ref(false)
const contactForm = ref({
  name: '',
  email: '',
  message: ''
})
const selectedFilter = ref('all') // 'all', 'inProgress', or 'completed'

// Computed property for project summary
const projectSummary = computed(() => {
  const totalProjects = projects.value.length
  const inProgressProjects = projects.value.filter(p => p.date.toLowerCase().includes('in progress')).length
  const completedProjects = totalProjects - inProgressProjects
  return {
    total: totalProjects,
    inProgress: inProgressProjects,
    completed: completedProjects
  }
})

// Computed property for filtered projects
const filteredProjects = computed(() => {
  if (selectedFilter.value === 'all') return projects.value
  const isInProgress = selectedFilter.value === 'inProgress'
  return projects.value.filter(p => 
    isInProgress ? 
    p.date.toLowerCase().includes('in progress') : 
    !p.date.toLowerCase().includes('in progress')
  )
})

// Watch for form changes and save to localStorage
watch(contactForm, (newValue) => {
  localStorage.setItem('contactForm', JSON.stringify(newValue))
}, { deep: true })

// Load saved form data on mount
if (typeof window !== 'undefined') {
  const savedForm = localStorage.getItem('contactForm')
  if (savedForm) {
    contactForm.value = JSON.parse(savedForm)
  }
}

// Projects data
const projects = ref([
  {
    id: 1,
    title: 'AI-Driven Carbon Footprint Reduction in Transportation',
    date: 'September 2024 (In Progress)',
    description: 'Developing an AI-based system to reduce the carbon footprint of transportation networks by optimizing logistics and energy usage.',
    details: [
      'Implementing machine learning models such as decision trees, random forests, and neural networks to analyze real-time data and propose actionable solutions to minimize environmental impact.',
      'Leveraging AI techniques such as pattern recognition and predictive modeling to enhance sustainability in transportation while formulating a framework to reduce CO2 emissions.'
    ]
  },
  {
    id: 2,
    title: 'Audio Stem Separation Using Hybrid Demucs',
    date: 'September 2024 (In Progress)',
    description: 'Building an AI-based tool for audio stem separation using the Hybrid Demucs model, focused on isolating drum and percussion components from songs.',
    details: [
      'Enhancing drum separation features by training the model on personal datasets and stems, expanding its accuracy and capabilities.',
      'Deploying the model on a server, accessible remotely via a website where users can upload audio files (WAV, MP3, FLAC, OGG/Vorbis) to receive individual drum stems.',
      'Leveraging cloud and remote access technologies like NordVPN Meshnet to ensure the server can be accessed from anywhere.'
    ]
  },
  {
    id: 3,
    title: 'Marine Snail Age Prediction via Machine Learning',
    date: 'April 2023',
    description: 'Led a 3-person team to develop a machine learning model to predict the age of abalone using physical measurements (weight, height, etc.) with features engineered for maximum accuracy.',
    details: [
      'Utilized algorithms such as regression models and decision trees, achieving a significant correlation between predictions and ages.'
    ]
  },
  {
    id: 4,
    title: 'Movie Recommendation System',
    date: 'March 2023',
    description: 'Designed and implemented a collaborative filtering-based recommendation system to suggest movies based on user ratings.',
    details: [
      'Employed matrix factorization to improve recommendation accuracy.',
      'Improved system performance by incorporating advanced filtering techniques.'
    ]
  },
  {
    id: 5,
    title: 'Hotel Booking Cancellation Prediction',
    date: 'February 2023',
    description: 'Analyzed booking data to determine factors contributing to cancellations, creating a predictive model for hotels to mitigate potential cancellations.',
    details: [
      'Utilized techniques like exploratory data analysis, logistic regression, support vector machines, and random forest regression.',
      'Created a predictive model that allows hotels to mitigate potential cancellations and optimize refund policies.'
    ]
  }
])

// Methods
const incrementCookies = () => {
  cookieCount.value++
}

const toggleAboutMe = () => {
  showAboutMe.value = !showAboutMe.value
}

const navigateToSection = (section: string) => {
  activeSection.value = section
}
</script>

<style scoped>
.app {
  max-width: 1200px;
  margin: 0 auto;
  padding: 2rem;
  color: #617C7E;
}

.nav-button {
  background: none;
  border: 2px solid transparent;
  color: #617C7E;
  padding: 0.5rem 1.5rem;
  margin-right: 1rem;
  cursor: pointer;
  font-size: 1.1rem;
  transition: all 0.3s ease;
  border-radius: 4px;
}

.nav-button:hover {
  background-color: #FBE0C3;
  color: #617C7E;
}

.nav-button.active {
  color: #617C7E;
  border-color: #FFB898;
  background-color: #FBE0C3;
}

nav {
  margin-bottom: 2rem;
  padding: 1rem 0;
  display: flex;
  justify-content: center;
  gap: 1rem;
  border-bottom: 2px solid #FFB898;
}

.section {
  margin: 2rem 0;
}

.btn {
  background-color: #617C7E;
  color: #FBE0C3;
  border: none;
  padding: 0.75rem 1.5rem;
  border-radius: 4px;
  cursor: pointer;
  font-size: 1rem;
  transition: background-color 0.3s;
}

.btn:hover {
  background-color: #4A6163;
}

.cookie-section {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 1rem;
  margin: 2rem 0;
}

.cookie-image {
  width: 200px;
  height: 200px;
  cursor: pointer;
  transition: transform 0.1s;
  border-radius: 50%;
  object-fit: cover;
}

.cookie-image:hover {
  transform: scale(1.05);
}

.cookie-image:active {
  transform: scale(0.95);
}

.cookie-count {
  font-size: 1.2rem;
  color: #617C7E;
  font-weight: 500;
}

.cookie-prompt {
  font-size: 1.4rem;
  color: #617C7E;
  font-weight: bold;
  margin-bottom: 1rem;
  animation: bounce 1s infinite;
}

.about-me {
  margin-top: 1rem;
  padding: 1.5rem;
  background: #5A6B71;
  border-radius: 8px;
  line-height: 1.6;
  animation: fadeIn 0.3s ease-in;
  color: #FBE0C3;
  box-shadow: 0 2px 4px rgba(52, 70, 72, 0.1);
}

.projects-list {
  display: flex;
  flex-direction: column;
  gap: 2rem;
  margin-top: 1rem;
  transition: all 0.3s ease;
}

.project-card {
  padding: 1.5rem;
  background: #5A6B71;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(52, 70, 72, 0.1);
  transition: all 0.3s ease;
  animation: slideIn 0.3s ease-out;
}

@keyframes slideIn {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.project-header {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  margin-bottom: 0.75rem;
  text-align: left;
  flex-wrap: wrap;
}

.project-header h3 {
  margin: 0;
  color: #FBE0C3;
  font-size: 1.75rem;
  font-weight: 600;
  line-height: 1.3;
}

.project-date {
  color: #FBE0C3;
  font-size: 1rem;
  align-self: flex-start;
  margin-top: 0.5rem;
}

.project-main-points,
.project-details {
  list-style-type: disc;
  margin: 0.5rem 0;
  padding-left: 1rem;
  color: #FBE0C3;
}

.project-description,
.project-details li {
  color: #FBE0C3;
  margin-bottom: 0.5rem;
  line-height: 1.5;
  padding-left: 0.25rem;
  text-align: left;
  display: list-item;
}

.project-details li:last-child {
  margin-bottom: 0;
}

.project-summary {
  margin-bottom: 1.5rem;
  padding: 1rem 1.5rem;
  background: #5A6B71;
  border-radius: 8px;
  color: #FBE0C3;
  display: flex;
  gap: 1.5rem;
  justify-content: center;
  align-items: center;
  box-shadow: 0 2px 4px rgba(52, 70, 72, 0.1);
}

.project-summary p {
  margin: 0;
  font-size: 1.1rem;
}

.filter-btn {
  background: transparent;
  border: 2px solid #FBE0C3;
  color: #FBE0C3;
  padding: 0.75rem 1.25rem;
  border-radius: 4px;
  cursor: pointer;
  font-size: 1.1rem;
  transition: all 0.3s ease;
  position: relative;
  z-index: 1;
  min-width: 180px;
  text-align: center;
}

.filter-btn:hover {
  border-color: #FFB898;
  transform: translateY(-1px);
}

.filter-btn.active {
  border-color: #FFB898;
  background: transparent;
  color: #FBE0C3;
  box-shadow: 0 0 8px rgba(255, 184, 152, 0.4);
  transform: translateY(-1px);
}

.contact-form {
  display: flex;
  flex-direction: column;
  gap: 1rem;
  max-width: 500px;
  margin: 0 auto;
}

.form-group {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
}

.form-group label {
  color: #617C7E;
  font-weight: 500;
}

.form-group input,
.form-group textarea {
  padding: 0.75rem;
  border: 1px solid #617C7E;
  border-radius: 4px;
  font-size: 1rem;
  background: #FBE0C3;
  color: #617C7E;
}

.form-group input:focus,
.form-group textarea:focus {
  outline: none;
  border-color: #617C7E;
  background: #FFB898;
}

@keyframes bounce {
  0%, 100% {
    transform: translateY(0);
  }
  50% {
    transform: translateY(-10px);
  }
}

@keyframes fadeIn {
  from { opacity: 0; }
  to { opacity: 1; }
}

.social-links {
  display: flex;
  gap: 1rem;
  justify-content: center;
  margin-bottom: 2rem;
}

.social-btn {
  display: inline-flex;
  align-items: center;
  gap: 0.5rem;
  padding: 0.75rem 1.25rem;
  border-radius: 8px;
  text-decoration: none;
  font-weight: 500;
  transition: all 0.3s ease;
  border: none;
  cursor: pointer;
}

.social-btn i {
  font-size: 1.2rem;
}

.resume-btn {
  background: #5A6B71;
  color: #FBE0C3;
  border: 2px solid #FBE0C3;
}

.resume-btn:hover {
  background: #FBE0C3;
  color: #5A6B71;
}

.github-btn {
  background: #333;
  color: #fff;
}

.github-btn:hover {
  background: #444;
  transform: translateY(-2px);
}

.linkedin-btn {
  background: #0077b5;
  color: #fff;
}

.linkedin-btn:hover {
  background: #0088cc;
  transform: translateY(-2px);
}
</style>
