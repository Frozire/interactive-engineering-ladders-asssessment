# Engineering Ladders Interactive Assessment

An interactive Vue.js application that implements the [Engineering Ladders framework](https://github.com/jorgef/engineeringladders) by Jorge Fernandez. This tool allows users to assess their skills across five key dimensions and discover which career profile matches their current level.

## Features

- **Interactive Assessment**: Rate yourself across 5 key axes (Technology, System, People, Process, Influence)
- **Career Profile Matching**: Get matched with specific career ladder positions based on your ratings
- **Visual Radar Chart**: See your profile visualized on a dynamic radar chart
- **Comprehensive Career Data**: Includes all major career paths:
  - Developer (D1-D7)
  - Tech Lead (TL4-TL7) 
  - Technical Program Manager (TPM4-TPM7)
  - Engineering Manager (EM5-EM7)

## The Framework

The Engineering Ladders framework evaluates professionals across 5 axes:

### 🔧 Technology
Knowledge of the tech stack and tools
- **Adopts**: Learns and adopts team-defined technology
- **Specializes**: Go-to person for specific technologies
- **Evangelizes**: Researches and introduces new technologies
- **Masters**: Deep knowledge of entire tech stack
- **Creates**: Designs widely-used new technologies

### 🏗️ System
Level of ownership of the system(s)
- **Enhances**: Delivers features and bug fixes
- **Designs**: Architects medium-large features
- **Owns**: Manages production operations and SLAs
- **Evolves**: Develops future architecture
- **Leads**: Drives technical excellence

### 👥 People
Relationship with team(s)
- **Learns**: Learns from others and steps up
- **Supports**: Proactively helps team members
- **Mentors**: Accelerates others' career growth
- **Coordinates**: Provides feedback and moderates
- **Manages**: Manages careers and team happiness

### ⚙️ Process
Engagement with development processes
- **Follows**: Adheres to team processes
- **Enforces**: Ensures process understanding
- **Challenges**: Seeks process improvements
- **Adjusts**: Guides process changes
- **Defines**: Establishes optimal processes

### 🌟 Influence
Scope of impact
- **Subsystem**: Impact on specific subsystems
- **Team**: Impact on entire team
- **Multiple Teams**: Cross-team influence
- **Company**: Organization-wide impact
- **Community**: Industry-wide influence

## Getting Started

### Prerequisites

- Node.js (version 16 or higher)
- npm or yarn

### Installation

1. Clone or download this repository
2. Install dependencies:
   ```bash
   npm install
   ```

### Development

Run the development server:
```bash
npm run dev
```

The application will be available at `http://localhost:5173`

### Building for Production

Create a production build:
```bash
npm run build
```

Preview the production build:
```bash
npm run preview
```

## How to Use

1. **Rate Yourself**: For each of the 5 axes (Technology, System, People, Process, Influence), select the level that best describes your current capabilities.

2. **View Matches**: The app will automatically calculate which career profiles match your selected levels. Profiles are ranked by match percentage.

3. **Visualize**: Your ratings are displayed on an interactive radar chart, giving you a visual representation of your current profile.

4. **Plan Growth**: Use the results to understand where you currently stand and what areas to focus on for career advancement.

## Understanding the Results

- **Match Percentage**: Shows how well your current level aligns with each career profile's requirements
- **Top Matches**: The app shows your top 3 career profile matches
- **Requirements**: Each profile shows the minimum level required for each axis
- **Radar Chart**: Visual representation helps identify strength and growth areas

## Career Ladder Paths

The tool includes comprehensive definitions for all major engineering career paths:

- **Developer Path**: Individual contributor focused on technical excellence (D1-D7)
- **Tech Lead Path**: Technical leadership with hands-on development (TL4-TL7)
- **Technical Program Manager**: Cross-team coordination and delivery (TPM4-TPM7)
- **Engineering Manager**: People management and team leadership (EM5-EM7)

## Technology Stack

- **Vue 3**: Modern JavaScript framework
- **Chart.js**: Interactive radar chart visualization
- **Vite**: Fast build tool and development server
- **Modern CSS**: Responsive design with glassmorphism effects

## Credits

Based on the excellent [Engineering Ladders framework](https://github.com/jorgef/engineeringladders) by Jorge Fernandez. This interactive tool brings the framework to life, making it easier for engineers and managers to conduct meaningful career conversations.

## License

This project is open source. The original Engineering Ladders framework is licensed under Apache-2.0.
