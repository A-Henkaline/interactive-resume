<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Adam Henkaline – Interactive Résumé</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdn.plot.ly/plotly-2.24.1.min.js"></script>
    <style>
        body {
            font-family: 'Inter', sans-serif;
            background-color: #f3f4f6;
            color: #1f2937;
        }
    </style>
</head>
<body class="p-6 md:p-12">
    <!-- Main Container -->
    <div class="max-w-6xl mx-auto bg-white shadow-xl rounded-lg p-8 space-y-8">
        <header class="text-center pb-4">
            <h1 class="text-4xl font-extrabold text-gray-900 mb-2">Adam Henkaline</h1>
            <p class="text-xl text-gray-600">Interactive Résumé</p>
            <div id="contact-info" class="text-sm text-gray-500 mt-2"></div>
        </header>

        <!-- Professional Summary -->
        <section class="p-6 bg-gray-50 rounded-lg shadow-inner">
            <h2 class="text-2xl font-bold text-gray-800 mb-4">Professional Summary</h2>
            <p id="summary" class="text-base text-gray-700 leading-relaxed"></p>
        </section>

        <!-- Career Timeline -->
        <section class="p-6 bg-gray-50 rounded-lg shadow-inner">
            <h2 class="text-2xl font-bold text-gray-800 mb-4">Career Timeline</h2>
            <div class="w-full md:w-1/2 mx-auto mb-6">
                <label for="company-filter" class="block text-gray-700 font-semibold mb-2">Filter by Company:</label>
                <select id="company-filter" class="w-full p-2 border border-gray-300 rounded-lg shadow-sm focus:outline-none focus:ring-2 focus:ring-blue-500 transition-all duration-200">
                    <option value="">All Companies</option>
                </select>
            </div>
            <div id="timeline-chart" class="w-full"></div>
        </section>
        
        <!-- Work Experience Details -->
        <section class="p-6 bg-gray-50 rounded-lg shadow-inner">
            <h2 class="text-2xl font-bold text-gray-800 mb-4">Work Experience</h2>
            <div id="experience-details" class="space-y-6"></div>
        </section>

        <!-- Top Skills -->
        <section class="p-6 bg-gray-50 rounded-lg shadow-inner">
            <h2 class="text-2xl font-bold text-gray-800 mb-4">Top Skills</h2>
            <div id="skills-chart" class="w-full"></div>
        </section>

        <!-- Education -->
        <section class="p-6 bg-gray-50 rounded-lg shadow-inner">
            <h2 class="text-2xl font-bold text-gray-800 mb-4">Education</h2>
            <ul id="education-list" class="space-y-2 text-gray-700"></ul>
        </section>
    </div>

    <script>
        window.onload = function() {
            // Full data embedded directly from the provided script
            const resumeData = [
                {"Section": "Contact", "Company": null, "Role": "Name", "Start": null, "End": null, "Description": "Adam Henkaline"},
                {"Section": "Contact", "Company": null, "Role": "Location", "Start": null, "End": null, "Description": "Buffalo, New York"},
                {"Section": "Contact", "Company": null, "Role": "Email", "Start": null, "End": null, "Description": "ahenkaline@gmail.com"},
                {"Section": "Contact", "Company": null, "Role": "LinkedIn", "Start": null, "End": null, "Description": "linkedin.com/in/adam-henkaline"},
                {
                    "Section": "Summary", "Company": null, "Role": null, "Start": null, "End": null,
                    "Description": "Strategic analytics leader with 15+ years of experience driving insight-based business transformation. Proven track record in leading high-impact data science initiatives, building predictive models, and aligning analytics roadmaps with enterprise strategy. Adept in managing cross-functional teams, translating complex data into executive-ready narratives, and scaling advanced analytics capabilities using tools like Python, SQL, Power BI, and Databricks. Known for building trusted partnerships across business functions and mentoring analytics talent to drive measurable outcomes."
                },
                {"Section": "Work Experience", "Company": "Rich Products Corporation", "Role": "Product Owner: Manager - Demand Creation Analytics", "Start": "08/2021", "End": "Present", "Description": "Led the development of a customer churn prediction model by integrating SAP and claims data using Databricks and Python."},
                {"Section": "Work Experience", "Company": "Rich Products Corporation", "Role": "Product Owner: Manager - Demand Creation Analytics", "Start": "08/2021", "End": "Present", "Description": "Designed a machine learning–based Digital Impact Index to quantify marketing influence and support revenue-driving decisions."},
                {"Section": "Work Experience", "Company": "Rich Products Corporation", "Role": "Product Owner: Manager - Demand Creation Analytics", "Start": "08/2021", "End": "Present", "Description": "Built Power BI + Power Automate framework to track report adoption, stakeholder engagement, and business impact."},
                {"Section": "Work Experience", "Company": "Rich Products Corporation", "Role": "Product Owner: Manager - Demand Creation Analytics", "Start": "08/2021", "End": "Present", "Description": "Created GPT-powered feature/story generator and toolkits for backlog and capacity tracking."},
                {"Section": "Work Experience", "Company": "Rich Products Corporation", "Role": "Product Owner: Manager - Demand Creation Analytics", "Start": "08/2021", "End": "Present", "Description": "Transformed a cross-functional team into hybrid BI developers/project managers."},
                {"Section": "Work Experience", "Company": "Rich Products Corporation", "Role": "Associate Manager - Demand Creation Analytics", "Start": "08/2021", "End": "Present", "Description": "Redesigned and migrated Power BI suite during transition from Dynamics to Salesforce."},
                {"Section": "Work Experience", "Company": "Rich Products Corporation", "Role": "Associate Manager - Demand Creation Analytics", "Start": "08/2021", "End": "Present", "Description": "Developed advanced Gross Margin analytics for strategic decisions."},
                {"Section": "Work Experience", "Company": "Rich Products Corporation", "Role": "Associate Manager - Demand Creation Analytics", "Start": "08/2021", "End": "Present", "Description": "Enhanced marketing analytics capabilities with improved accessibility and KPI tracking."},
                {"Section": "Work Experience", "Company": "Rich Products Corporation", "Role": "Associate Manager - Demand Creation Analytics", "Start": "08/2021", "End": "Present", "Description": "Awarded 'Rookie of the Year' for strong business impact."},
                {"Section": "Work Experience", "Company": "Freed Maxick CPAs, P.C.", "Role": "Senior Analyst", "Start": "12/2020", "End": "08/2021", "Description": "Audited financial documents to support COVID-era tax rebate programs."},
                {"Section": "Work Experience", "Company": "Freed Maxick CPAs, P.C.", "Role": "Senior Analyst", "Start": "12/2020", "End": "08/2021", "Description": "Developed Excel-based reporting processes and performed ad hoc analysis."},
                {"Section": "Work Experience", "Company": "Delaware North", "Role": "Revenue Management Strategy Analyst", "Start": "02/2020", "End": "07/2020", "Description": "Provided business insight and operational support for revenue management."},
                {"Section": "Work Experience", "Company": "Delaware North", "Role": "Revenue Management Strategy Analyst", "Start": "02/2020", "End": "07/2020", "Description": "Conducted complex data analysis to support business initiatives."},
                {"Section": "Work Experience", "Company": "XCM Solutions, LLC", "Role": "Data Analyst", "Start": "07/2018", "End": "01/2020", "Description": "Standardized metrics and automated reporting tools to improve customer experience."},
                {"Section": "Work Experience", "Company": "XCM Solutions, LLC", "Role": "Data Analyst", "Start": "07/2018", "End": "01/2020", "Description": "Implemented analytics strategies to improve productivity and outcomes."},
                {"Section": "Work Experience", "Company": "XCM Solutions, LLC", "Role": "Data Analyst", "Start": "07/2018", "End": "01/2020", "Description": "Delivered insights through impactful visualizations informing business decisions."},
                {"Section": "Work Experience", "Company": "Army National Guard", "Role": "Captain", "Start": "04/2003", "End": "04/2018", "Description": "Led and mentored 180+ personnel in operations, training, and logistics."},
                {"Section": "Work Experience", "Company": "Army National Guard", "Role": "Captain", "Start": "04/2003", "End": "04/2018", "Description": "Planned mobilization of 600 troops for Kosovo mission."},
                {"Section": "Work Experience", "Company": "Army National Guard", "Role": "Captain", "Start": "04/2003", "End": "04/2018", "Description": "Oversaw $100M+ in assets including weapons systems and vehicles."},
                {"Section": "Work Experience", "Company": "Army National Guard", "Role": "Captain", "Start": "04/2003", "End": "04/2018", "Description": "Completed deployments to Kosovo (x2), Iraq, Afghanistan, and Hurricane Katrina response."},
                {"Section": "Work Experience", "Company": "Army National Guard", "Role": "Captain", "Start": "04/2003", "End": "04/2018", "Description": "Progressed from enlisted to commissioned officer; held roles including Commander and Operations Officer."},
                {"Section": "Work Experience", "Company": "United States Department of Defense", "Role": "Recruiting Operations Officer", "Start": "10/2012", "End": "08/2017", "Description": "Led ROTC recruiting marketing strategy, managing $82K budget."},
                {"Section": "Work Experience", "Company": "United States Department of Defense", "Role": "Recruiting Operations Officer", "Start": "10/2012", "End": "08/2017", "Description": "Oversaw scholarship processing and onboarding for recruiting pipeline."},
                {"Section": "Work Experience", "Company": "United States Department of Defense", "Role": "Assistant Professor of Military Science", "Start": "10/2012", "End": "08/2017", "Description": "Designed and delivered leadership curriculum for 50+ cadets."},
                {"Section": "Work Experience", "Company": "United States Department of Defense", "Role": "Assistant Professor of Military Science", "Start": "10/2012", "End": "08/2017", "Description": "Managed ROTC program ops, training logistics, and cadet readiness."},
                {"Section": "Education", "Company": "Northeastern University", "Role": "Master of Professional Studies in Analytics", "Start": "01/2018", "End": "01/2020", "Description": "Boston, MA"},
                {"Section": "Education", "Company": "The Ohio State University", "Role": "Bachelor of Arts in Criminology", "Start": "01/2007", "End": "01/2010", "Description": "Columbus, OH"},
                {"Section": "Skills", "Company": null, "Role": null, "Start": null, "End": null, "Description": "Actionable insights"},
                {"Section": "Skills", "Company": null, "Role": null, "Start": null, "End": null, "Description": "Analytical"},
                {"Section": "Skills", "Company": null, "Role": null, "Start": null, "End": null, "Description": "Analytics and reporting"},
                {"Section": "Skills", "Company": null, "Role": null, "Start": null, "End": null, "Description": "Business Process"},
                {"Section": "Skills", "Company": null, "Role": null, "Start": null, "End": null, "Description": "Collaborating"},
                {"Section": "Skills", "Company": null, "Role": null, "Start": null, "End": null, "Description": "Critical Thinking"},
                {"Section": "Skills", "Company": null, "Role": null, "Start": null, "End": null, "Description": "Data Extraction"},
                {"Section": "Skills", "Company": null, "Role": null, "Start": null, "End": null, "Description": "Data Modeling"},
                {"Section": "Skills", "Company": null, "Role": null, "Start": null, "End": null, "Description": "Data Science"},
                {"Section": "Skills", "Company": null, "Role": null, "Start": null, "End": null, "Description": "Decision-Making"},
                {"Section": "Skills", "Company": null, "Role": null, "Start": null, "End": null, "Description": "Direct report"},
                {"Section": "Skills", "Company": null, "Role": null, "Start": null, "End": null, "Description": "KPIs"},
                {"Section": "Skills", "Company": null, "Role": null, "Start": null, "End": null, "Description": "Mentorship"},
                {"Section": "Skills", "Company": null, "Role": null, "Start": null, "End": null, "Description": "Operations Management"},
                {"Section": "Skills", "Company": null, "Role": null, "Start": null, "End": null, "Description": "Reporting and analysis"},
                {"Section": "Skills", "Company": null, "Role": null, "Start": null, "End": null, "Description": "Retention"},
                {"Section": "Skills", "Company": null, "Role": null, "Start": null, "End": null, "Description": "Strategic Planning"},
                {"Section": "Skills", "Company": null, "Role": null, "Start": null, "End": null, "Description": "Team Leadership"},
                {"Section": "Skills", "Company": null, "Role": null, "Start": null, "End": null, "Description": "Test-and-learn"},
                {"Section": "Skills", "Company": null, "Role": null, "Start": null, "End": null, "Description": "Training & Staff Development"}
            ];

            const workData = resumeData.filter(d => d.Section === "Work Experience").map(d => ({
                ...d,
                Start: new Date(d.Start.replace(/(\d{2})\/(\d{4})/, '$2-$1-01')),
                End: d.End === "Present" ? new Date() : new Date(d.End.replace(/(\d{2})\/(\d{4})/, '$2-$1-01'))
            }));
            const skillsData = resumeData.filter(d => d.Section === "Skills");
            const educationData = resumeData.filter(d => d.Section === "Education");
            const summaryData = resumeData.find(d => d.Section === "Summary");
            const contactData = resumeData.filter(d => d.Section === "Contact");

            // Populate Contact Info
            const contactDiv = document.getElementById('contact-info');
            contactDiv.innerHTML = contactData.map(d => {
                if (d.Role === "Name") return '';
                if (d.Role === "LinkedIn") {
                    return `<span><a href="https://${d.Description}" target="_blank" class="text-blue-500 hover:underline">${d.Description}</a></span>`;
                }
                return `<span>${d.Description}</span>`;
            }).filter(Boolean).join(' | ');

            // Populate Summary Section
            document.getElementById('summary').textContent = summaryData.Description;

            // Populate Education Section
            const educationList = document.getElementById('education-list');
            educationData.forEach(edu => {
                const li = document.createElement('li');
                li.textContent = `${edu.Role} – ${edu.Company} (${edu.Start.split('/')[1]} to ${edu.End.split('/')[1]}) - ${edu.Description}`;
                educationList.appendChild(li);
            });

            // Populate Work Experience Details
            const experienceDetails = document.getElementById('experience-details');
            const companies = [...new Set(workData.map(d => d.Company))];
            companies.forEach(company => {
                const companyData = workData.filter(d => d.Company === company);
                const companyDiv = document.createElement('div');
                companyDiv.className = 'bg-white p-4 rounded-lg shadow-sm mb-4';
                companyDiv.innerHTML = `
                    <h3 class="text-xl font-semibold text-gray-800">${companyData[0].Company}</h3>
                    <p class="text-gray-600 font-medium mb-2">${companyData[0].Role} (${companyData[0].Start.toLocaleDateString()} - ${companyData[0].End.toLocaleDateString()})</p>
                    <ul class="list-disc list-inside space-y-1 text-gray-700">
                        ${companyData.map(d => `<li>${d.Description}</li>`).join('')}
                    </ul>
                `;
                experienceDetails.appendChild(companyDiv);
            });

            // Populate Company Filter Dropdown
            const companyFilter = document.getElementById('company-filter');
            const uniqueCompanies = [...new Set(workData.map(d => d.Company))];
            uniqueCompanies.forEach(company => {
                const option = document.createElement('option');
                option.value = company;
                option.textContent = company;
                companyFilter.appendChild(option);
            });
            companyFilter.addEventListener('change', updateTimeline);

            // Draw Skills Chart
            function drawSkillsChart() {
                const skillCounts = skillsData.reduce((acc, d) => {
                    acc[d.Description] = (acc[d.Description] || 0) + 1;
                    return acc;
                }, {});

                const sortedSkills = Object.entries(skillCounts).sort(([, a], [, b]) => a - b);
                const skills = sortedSkills.map(([skill]) => skill);
                const counts = sortedSkills.map(([, count]) => count);

                const skillsTrace = {
                    y: skills,
                    x: counts,
                    type: 'bar',
                    orientation: 'h',
                    marker: { color: 'rgb(59, 130, 246)' }
                };
                const skillsLayout = {
                    title: 'Top Skills',
                    xaxis: { title: 'Mentions' },
                    yaxis: { title: 'Skill' },
                    margin: { t: 50, l: 150, r: 50, b: 50 },
                    responsive: true,
                    paper_bgcolor: 'rgba(0,0,0,0)',
                    plot_bgcolor: 'rgba(0,0,0,0)'
                };
                Plotly.newPlot('skills-chart', [skillsTrace], skillsLayout);
            }

            // Draw Timeline Chart
            function updateTimeline(event) {
                const selectedCompany = event ? event.target.value : '';
                const filteredData = selectedCompany ? workData.filter(d => d.Company === selectedCompany) : workData;

                const plotData = filteredData.map(d => ({
                    type: 'scatter',
                    mode: 'lines',
                    x: [d.Start, d.End],
                    y: [d.Company, d.Company],
                    line: { color: '#3b82f6', width: 15 },
                    name: d.Role,
                    hoverinfo: 'text',
                    text: `${d.Role}<br>${d.Description}`
                }));

                Plotly.newPlot('timeline-chart', plotData, {
                    title: 'Career Timeline',
                    xaxis: { type: 'date', title: 'Date' },
                    yaxis: { autorange: 'reversed', title: 'Company' },
                    showlegend: false,
                    margin: { t: 50, l: 150, r: 50, b: 50 },
                    responsive: true,
                    paper_bgcolor: 'rgba(0,0,0,0)',
                    plot_bgcolor: 'rgba(0,0,0,0)'
                });
            }

            drawSkillsChart();
            updateTimeline();
        };
    </script>
</body>
</html>
