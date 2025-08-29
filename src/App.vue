<template>
  <div id="app">
    <div class="container">
      <header class="header">
        <h1>Engineering Ladders Interactive Assessment</h1>
        <p>Select your level for each axis to discover your career profile</p>
      </header>

      <div class="assessment-grid">
        <!-- Assessment Form -->
        <div class="assessment-form">
          <h2>Rate Your Skills</h2>
          
          <div v-for="axis in axes" :key="axis.name" class="axis-section">
            <h3>{{ axis.name }}</h3>
            <p class="axis-description">{{ axis.description }}</p>
            
            <div class="level-options">
              <label v-for="(level, index) in axis.levels" :key="index" class="level-option">
                <input 
                  type="radio" 
                  :name="axis.name.toLowerCase()" 
                  :value="index + 1" 
                  v-model="selectedLevels[axis.name.toLowerCase()]"
                  @change="updateProfile"
                />
                <span class="level-number">{{ index + 1 }}</span>
                <span class="level-name">{{ level.name }}</span>
                <span class="level-desc">{{ level.description }}</span>
              </label>
            </div>
          </div>
        </div>

        <!-- Results -->
        <div class="results-panel">
          <h2>Your Profile</h2>
          
          <div v-if="matchedProfiles.length > 0" class="matched-profiles">
                      <div v-for="profile in matchedProfiles" :key="profile.id" class="profile-match">
            <h3>{{ profile.title }}</h3>
            <div class="match-info">
              <div class="match-score">{{ Math.round(profile.matchScore * 100) }}% Match</div>
              <div class="requirements-met">{{ profile.metRequirements }}/{{ profile.totalRequirements }} Requirements Met</div>
              <div v-if="profile.fullyQualified" class="qualification-status qualified">✅ Fully Qualified</div>
              <div v-else class="qualification-status working-toward">🎯 Working Toward</div>
            </div>
            <p>{{ profile.description }}</p>
            
            <div class="profile-requirements">
              <h4>Level Requirements:</h4>
              <div class="requirements-grid">
                <div v-for="(requirement, axis) in profile.requirements" :key="axis" 
                     :class="['requirement', { 'met': selectedLevels[axis] >= requirement, 'close': selectedLevels[axis] === requirement - 1 }]">
                  <span class="axis-name">{{ axis }}:</span>
                  <span class="required-level">{{ requirement }}+</span>
                  <span v-if="selectedLevels[axis]" class="current-level">(you: {{ selectedLevels[axis] }})</span>
                </div>
              </div>
            </div>
          </div>
          </div>

          <div v-else class="no-match">
            <p>Complete the assessment to see your career profile matches.</p>
          </div>

          <!-- Radar Chart -->
          <div v-if="hasSelection" class="chart-container">
            <canvas ref="chartCanvas" width="300" height="300"></canvas>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { Chart, RadarController, RadialLinearScale, PointElement, LineElement, Filler, Tooltip, Legend } from 'chart.js'

// Register Chart.js components
Chart.register(RadarController, RadialLinearScale, PointElement, LineElement, Filler, Tooltip, Legend)

export default {
  name: 'App',
  data() {
    return {
      selectedLevels: {
        technology: null,
        system: null,
        people: null,
        process: null,
        influence: null
      },
      chart: null,
      axes: [
        {
          name: 'Technology',
          description: 'Knowledge of the tech stack and tools',
          levels: [
            { name: 'Adopts', description: 'Actively learns and adopts technology defined by the team' },
            { name: 'Specializes', description: 'Go-to person for technologies, takes initiative to learn new ones' },
            { name: 'Evangelizes', description: 'Researches and introduces new technologies to the team' },
            { name: 'Masters', description: 'Deep knowledge about the whole technology stack' },
            { name: 'Creates', description: 'Designs and creates new technologies widely used by teams' }
          ]
        },
        {
          name: 'System',
          description: 'Level of ownership of the system(s)',
          levels: [
            { name: 'Enhances', description: 'Pushes new features and bug fixes to improve the system' },
            { name: 'Designs', description: 'Designs medium to large features while reducing tech debt' },
            { name: 'Owns', description: 'Owns production operation, monitoring, and system SLAs' },
            { name: 'Evolves', description: 'Evolves architecture for future requirements and defines SLAs' },
            { name: 'Leads', description: 'Leads technical excellence and creates outage mitigation plans' }
          ]
        },
        {
          name: 'People',
          description: 'Relationship with the team(s)',
          levels: [
            { name: 'Learns', description: 'Learns from others and steps up when required' },
            { name: 'Supports', description: 'Proactively supports team members and helps them succeed' },
            { name: 'Mentors', description: 'Mentors others to accelerate career growth' },
            { name: 'Coordinates', description: 'Coordinates team members with effective feedback' },
            { name: 'Manages', description: 'Manages career, expectations, performance and happiness' }
          ]
        },
        {
          name: 'Process',
          description: 'Level of engagement with the development process',
          levels: [
            { name: 'Follows', description: 'Follows team processes, delivers consistent flow of features' },
            { name: 'Enforces', description: 'Enforces team processes, ensures understanding of tradeoffs' },
            { name: 'Challenges', description: 'Challenges team processes, looks for improvements' },
            { name: 'Adjusts', description: 'Adjusts team processes, guides team through changes' },
            { name: 'Defines', description: 'Defines right processes for team maturity level' }
          ]
        },
        {
          name: 'Influence',
          description: 'Scope of influence of the position',
          levels: [
            { name: 'Subsystem', description: 'Makes impact on one or more subsystems' },
            { name: 'Team', description: 'Makes impact on the whole team' },
            { name: 'Multiple Teams', description: 'Makes impact on multiple teams' },
            { name: 'Company', description: 'Makes impact on the whole tech organization' },
            { name: 'Community', description: 'Makes impact on the tech community' }
          ]
        }
      ],
      careerProfiles: [
        // Developer Levels
        {
          id: 'd1',
          title: 'D1 - Developer 1',
          description: 'Entry-level developer learning the fundamentals',
          requirements: { technology: 1, system: 1, people: 1, process: 1, influence: 1 }
        },
        {
          id: 'd2',
          title: 'D2 - Developer 2',
          description: 'Junior developer with some experience',
          requirements: { technology: 2, system: 1, people: 1, process: 1, influence: 1 }
        },
        {
          id: 'd3',
          title: 'D3 - Developer 3',
          description: 'Mid-level developer with solid technical skills',
          requirements: { technology: 2, system: 1, people: 1, process: 1, influence: 1 }
        },
        {
          id: 'd4',
          title: 'D4 - Developer 4 (Senior)',
          description: 'Senior developer with strong technical expertise',
          requirements: { technology: 2, system: 2, people: 2, process: 2, influence: 2 }
        },
        {
          id: 'd5',
          title: 'D5 - Developer 5 (Senior)',
          description: 'Senior developer with advanced technical skills',
          requirements: { technology: 3, system: 2, people: 2, process: 2, influence: 2 }
        },
        {
          id: 'd6',
          title: 'D6 - Developer 6 (Senior)',
          description: 'Staff-level developer with deep expertise',
          requirements: { technology: 3, system: 3, people: 2, process: 2, influence: 3 }
        },
        {
          id: 'd7',
          title: 'D7 - Developer 7 (Senior)',
          description: 'Principal developer with exceptional technical leadership',
          requirements: { technology: 4, system: 3, people: 3, process: 3, influence: 3 }
        },
        
        // Tech Lead Levels
        {
          id: 'tl4',
          title: 'TL4 - Tech Lead 4',
          description: 'Entry-level tech lead balancing coding and technical leadership',
          requirements: { technology: 3, system: 3, people: 2, process: 2, influence: 2 }
        },
        {
          id: 'tl5',
          title: 'TL5 - Tech Lead 5',
          description: 'Experienced tech lead with system ownership',
          requirements: { technology: 3, system: 3, people: 3, process: 3, influence: 3 }
        },
        {
          id: 'tl6',
          title: 'TL6 - Tech Lead 6',
          description: 'Senior tech lead with architectural responsibility',
          requirements: { technology: 4, system: 4, people: 3, process: 3, influence: 3 }
        },
        {
          id: 'tl7',
          title: 'TL7 - Tech Lead 7',
          description: 'Principal tech lead driving technical excellence',
          requirements: { technology: 4, system: 4, people: 3, process: 4, influence: 4 }
        },
        
        // Technical Program Manager Levels
        {
          id: 'tpm4',
          title: 'TPM4 - Technical Program Manager 4',
          description: 'Entry-level TPM coordinating cross-team initiatives',
          requirements: { technology: 2, system: 2, people: 3, process: 3, influence: 3 }
        },
        {
          id: 'tpm5',
          title: 'TPM5 - Technical Program Manager 5',
          description: 'Experienced TPM managing complex programs',
          requirements: { technology: 2, system: 3, people: 3, process: 4, influence: 3 }
        },
        {
          id: 'tpm6',
          title: 'TPM6 - Technical Program Manager 6',
          description: 'Senior TPM driving organization-wide initiatives',
          requirements: { technology: 3, system: 3, people: 4, process: 4, influence: 4 }
        },
        {
          id: 'tpm7',
          title: 'TPM7 - Technical Program Manager 7',
          description: 'Principal TPM leading strategic technical programs',
          requirements: { technology: 3, system: 4, people: 4, process: 5, influence: 4 }
        },
        
        // Engineering Manager Levels
        {
          id: 'em5',
          title: 'EM5 - Engineering Manager 5',
          description: 'Entry-level engineering manager leading a team',
          requirements: { technology: 2, system: 2, people: 4, process: 3, influence: 2 }
        },
        {
          id: 'em6',
          title: 'EM6 - Engineering Manager 6',
          description: 'Experienced engineering manager with multiple teams',
          requirements: { technology: 2, system: 3, people: 5, process: 4, influence: 3 }
        },
        {
          id: 'em7',
          title: 'EM7 - Engineering Manager 7',
          description: 'Senior engineering manager leading organization',
          requirements: { technology: 3, system: 3, people: 5, process: 4, influence: 4 }
        }
      ]
    }
  },
  computed: {
    hasSelection() {
      return Object.values(this.selectedLevels).some(level => level !== null)
    },
    matchedProfiles() {
      if (!this.hasSelection) return []
      
      const matches = this.careerProfiles.map(profile => {
        let matchScore = 0
        let totalAxes = 0
        let metRequirements = 0
        
        for (const [axis, requiredLevel] of Object.entries(profile.requirements)) {
          const selectedLevel = this.selectedLevels[axis]
          if (selectedLevel !== null) {
            totalAxes++
            if (selectedLevel >= requiredLevel) {
              matchScore += 1
              metRequirements++
            } else {
              // More generous partial credit for being close
              const deficit = requiredLevel - selectedLevel
              if (deficit === 1) {
                matchScore += 0.7 // 70% credit for being 1 level away
              } else if (deficit === 2) {
                matchScore += 0.4 // 40% credit for being 2 levels away
              } else {
                matchScore += 0.1 // 10% credit for being further away
              }
            }
          }
        }
        
        return {
          ...profile,
          matchScore: totalAxes > 0 ? matchScore / totalAxes : 0,
          metRequirements,
          totalRequirements: Object.keys(profile.requirements).length,
          fullyQualified: metRequirements === Object.keys(profile.requirements).length
        }
      }).filter(profile => profile.matchScore > 0.4)

      // Group profiles by career track
      const trackGroups = {
        developer: matches.filter(p => p.id.startsWith('d')),
        techLead: matches.filter(p => p.id.startsWith('tl')),
        tpm: matches.filter(p => p.id.startsWith('tpm')),
        engineeringManager: matches.filter(p => p.id.startsWith('em'))
      }

      // For each track, find the highest level they fully qualify for
      // and include that plus potentially the next level they're working toward
      const filteredMatches = []
      
      Object.values(trackGroups).forEach(trackProfiles => {
        if (trackProfiles.length === 0) return
        
        // Sort by level (extract number from ID)
        const sortedProfiles = trackProfiles.sort((a, b) => {
          const aLevel = parseInt(a.id.match(/\d+/)[0])
          const bLevel = parseInt(b.id.match(/\d+/)[0])
          return aLevel - bLevel
        })
        
        // Find highest level they fully qualify for
        let highestQualified = null
        let nextLevel = null
        
        for (let i = sortedProfiles.length - 1; i >= 0; i--) {
          if (sortedProfiles[i].fullyQualified && !highestQualified) {
            highestQualified = sortedProfiles[i]
            // Also include the next level if it exists and they're working toward it
            if (i < sortedProfiles.length - 1) {
              nextLevel = sortedProfiles[i + 1]
            }
            break
          }
        }
        
        // If they don't fully qualify for any level in this track,
        // show the level they're closest to (highest match score)
        if (!highestQualified && sortedProfiles.length > 0) {
          const bestMatch = sortedProfiles.reduce((best, current) => 
            current.matchScore > best.matchScore ? current : best
          )
          if (bestMatch.matchScore > 0.5) { // Only if reasonably close
            nextLevel = bestMatch
          }
        }
        
        // Add the profiles to show
        if (highestQualified) {
          filteredMatches.push(highestQualified)
        }
        if (nextLevel && (!highestQualified || nextLevel.id !== highestQualified.id)) {
          filteredMatches.push(nextLevel)
        }
      })
      
      // Sort final results by match score, then by level (prefer lower levels for ties)
      return filteredMatches
        .sort((a, b) => {
          // First sort by match score (highest first)
          if (Math.abs(a.matchScore - b.matchScore) > 0.001) {
            return b.matchScore - a.matchScore
          }
          // If match scores are essentially equal, prefer lower levels
          const aLevel = parseInt(a.id.match(/\d+/)?.[0] || '999')
          const bLevel = parseInt(b.id.match(/\d+/)?.[0] || '999')
          return aLevel - bLevel
        })
        .slice(0, 6) // Show up to 6 matches
    }
  },
  beforeUnmount() {
    // Clean up chart when component is destroyed
    if (this.chart) {
      try {
        this.chart.destroy()
      } catch (error) {
        console.warn('Error destroying chart on unmount:', error)
      }
    }
  },
  methods: {
    updateProfile() {
      this.$nextTick(() => {
        this.updateChart()
      })
    },
    updateChart() {
      if (!this.$refs.chartCanvas) return
      
      const ctx = this.$refs.chartCanvas.getContext('2d')
      
      // Safely destroy existing chart
      if (this.chart) {
        try {
          this.chart.destroy()
        } catch (error) {
          console.warn('Error destroying chart:', error)
        }
        this.chart = null
      }
      
      const data = {
        labels: ['Technology', 'System', 'People', 'Process', 'Influence'],
        datasets: [{
          label: 'Your Profile',
          data: [
            this.selectedLevels.technology || 0,
            this.selectedLevels.system || 0,
            this.selectedLevels.people || 0,
            this.selectedLevels.process || 0,
            this.selectedLevels.influence || 0
          ],
          backgroundColor: 'rgba(102, 126, 234, 0.2)',
          borderColor: 'rgba(102, 126, 234, 1)',
          borderWidth: 2,
          pointBackgroundColor: 'rgba(102, 126, 234, 1)',
          pointBorderColor: '#fff',
          pointHoverBackgroundColor: '#fff',
          pointHoverBorderColor: 'rgba(102, 126, 234, 1)'
        }]
      }
      
      try {
        this.chart = new Chart(ctx, {
          type: 'radar',
          data: data,
          options: {
            responsive: true,
            maintainAspectRatio: false,
            animation: {
              duration: 200
            },
            scales: {
              r: {
                beginAtZero: true,
                max: 5,
                ticks: {
                  stepSize: 1
                }
              }
            },
            plugins: {
              legend: {
                display: false
              }
            }
          }
        })
      } catch (error) {
        console.error('Error creating chart:', error)
      }
    }
  }
}
</script>

<style>
* {
  box-sizing: border-box;
}

.container {
  max-width: 1400px;
  margin: 0 auto;
  padding: 20px;
  color: white;
}

.header {
  text-align: center;
  margin-bottom: 40px;
}

.header h1 {
  font-size: 2.5rem;
  margin-bottom: 10px;
  text-shadow: 2px 2px 4px rgba(0,0,0,0.3);
}

.header p {
  font-size: 1.1rem;
  opacity: 0.9;
}

.assessment-grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 40px;
  align-items: start;
}

@media (max-width: 768px) {
  .assessment-grid {
    grid-template-columns: 1fr;
    gap: 20px;
  }
}

.assessment-form,
.results-panel {
  background: rgba(255, 255, 255, 0.1);
  backdrop-filter: blur(10px);
  border-radius: 15px;
  padding: 30px;
  box-shadow: 0 8px 32px rgba(0,0,0,0.2);
}

.assessment-form h2,
.results-panel h2 {
  margin-top: 0;
  font-size: 1.8rem;
  margin-bottom: 25px;
}

.axis-section {
  margin-bottom: 35px;
}

.axis-section h3 {
  color: #fff;
  font-size: 1.3rem;
  margin-bottom: 8px;
}

.axis-description {
  opacity: 0.8;
  margin-bottom: 15px;
  font-size: 0.95rem;
}

.level-options {
  display: flex;
  flex-direction: column;
  gap: 10px;
}

.level-option {
  display: flex;
  align-items: center;
  gap: 12px;
  padding: 12px;
  background: rgba(255, 255, 255, 0.1);
  border-radius: 8px;
  cursor: pointer;
  transition: all 0.3s ease;
}

.level-option:hover {
  background: rgba(255, 255, 255, 0.2);
  transform: translateX(5px);
}

.level-option input[type="radio"] {
  margin: 0;
}

.level-number {
  background: rgba(255, 255, 255, 0.2);
  color: white;
  border-radius: 50%;
  width: 24px;
  height: 24px;
  display: flex;
  align-items: center;
  justify-content: center;
  font-weight: bold;
  font-size: 0.9rem;
}

.level-name {
  font-weight: bold;
  min-width: 100px;
}

.level-desc {
  flex: 1;
  opacity: 0.9;
  font-size: 0.9rem;
}

.matched-profiles {
  margin-bottom: 30px;
}

.profile-match {
  background: rgba(255, 255, 255, 0.1);
  border-radius: 10px;
  padding: 20px;
  margin-bottom: 20px;
}

.profile-match h3 {
  margin-top: 0;
  margin-bottom: 10px;
  color: #fff;
}

.match-info {
  display: flex;
  gap: 10px;
  margin-bottom: 10px;
  align-items: center;
  flex-wrap: wrap;
}

.match-score {
  background: linear-gradient(45deg, #4CAF50, #45a049);
  color: white;
  padding: 4px 12px;
  border-radius: 20px;
  font-size: 0.85rem;
  font-weight: bold;
}

.requirements-met {
  background: rgba(255, 255, 255, 0.2);
  color: white;
  padding: 4px 12px;
  border-radius: 20px;
  font-size: 0.85rem;
  font-weight: bold;
}

.qualification-status {
  padding: 4px 12px;
  border-radius: 20px;
  font-size: 0.85rem;
  font-weight: bold;
}

.qualification-status.qualified {
  background: rgba(76, 175, 80, 0.8);
  color: white;
}

.qualification-status.working-toward {
  background: rgba(255, 152, 0, 0.8);
  color: white;
}

.profile-requirements h4 {
  margin: 15px 0 10px 0;
  font-size: 1rem;
}

.requirements-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(120px, 1fr));
  gap: 8px;
}

.requirement {
  background: rgba(255, 255, 255, 0.1);
  padding: 8px 12px;
  border-radius: 6px;
  font-size: 0.9rem;
  transition: all 0.3s ease;
}

.requirement.met {
  background: rgba(76, 175, 80, 0.3);
  border: 1px solid rgba(76, 175, 80, 0.5);
}

.requirement.close {
  background: rgba(255, 193, 7, 0.3);
  border: 1px solid rgba(255, 193, 7, 0.5);
}

.axis-name {
  font-weight: bold;
}

.required-level {
  color: #4CAF50;
  font-weight: bold;
}

.current-level {
  color: #E3F2FD;
  font-size: 0.8rem;
  opacity: 0.8;
}

.no-match {
  text-align: center;
  opacity: 0.7;
  padding: 40px 20px;
}

.chart-container {
  margin-top: 30px;
  background: rgba(255, 255, 255, 0.1);
  border-radius: 10px;
  padding: 20px;
  height: 320px;
}

.chart-container canvas {
  max-width: 100%;
  height: auto !important;
}
</style>
