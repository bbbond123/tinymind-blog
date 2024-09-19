---
title: Automating Daily Technology News Digest with CrewAI and Markdown Generation
date: 2024-09-19T03:38:42.984Z
---

Introduction
In the ever-evolving world of technology, staying updated with the latest news can be overwhelming. To simplify this process, I’ve employed crewAI to automate the collection and organization of daily technology news into an easily consumable Markdown format. This blog post details how to set up agents and tasks within the CrewAI framework to achieve this automation efficiently.

System Design
Our system will perform the following tasks:

Scrape news articles from popular technology sources such as Medium, blogs, and YouTube. Initially, we’ll focus on simpler sources to refine our methodology.
Digest and compile the information into a Markdown file, ensuring it’s well-organized and accessible.
Support multilingual content, with an emphasis on including Chinese language news, to broaden the accessibility of our news digest.
Implementation Steps
Here’s how we can set up the CrewAI environment to handle our requirements:

Step 1: Agent Setup for News Collection
First, we’ll set up a News Collector Agent to scrape technology news from various sources. We'll utilize SerperDevTool for scraping:

from crewai import Agent, Task
from crewai_tools import SerperDevTool

search_tool = SerperDevTool()
news_collector = Agent(
    role='Technology News Harvester',
    goal='Collect daily technology news from various sources on a specific topic.',
    tools=[search_tool],
    verbose=True,
    memory=True,
    backstory=(
        "Dedicated to scouring the internet for the latest tech news, "
        "you aim to gather the most relevant articles to keep the tech community well-informed."
    ),
    allow_delegation=True
)
fetch_news_task = Task(
    description=(
        "Scrape the latest technology articles related to the chosen topic from various sources. "
        "Summarize key points and prepare data for the next processing stage."
    ),
    expected_output='Raw data ready for processing.',
    agent=news_collector
)
Step 2: Organizing Data
Next, a Content Organizer Agent processes the scraped data into a structured format suitable for Markdown conversion:

content_organizer = Agent(
    role='Data Organizer',
    goal='Convert raw news data into a structured format for reporting.',
    backstory=(
        "Expert in data manipulation, you transform unstructured data into organized, concise formats. "
        "Your work ensures that the information is not only accurate but also pleasing to read."
    ),
    allow_delegation=True
)

organize_data_task = Task(
    description=(
        "Take raw data from the news collector and organize it into a structured format. "
        "Extract and highlight headlines, publication dates, and summaries."
    ),
    expected_output='Organized content ready for Markdown formatting.',
    agent=content_organizer
)
Step 3: Markdown Compilation
Finally, the Markdown Generator Agent takes the organized data and compiles it into a Markdown document:

markdown_generator = Agent(
    role='Report Compiler',
    goal='Generate a Markdown document from structured news data.',
    backstory=(
        "As a compiler of information, your role is to take organized data and weave it into a coherent, "
        "engaging narrative in Markdown format."
    ),
    allow_delegation=True
)

generate_markdown_task = Task(
    description='Compile the structured data into a Markdown formatted document.',
    expected_output='A well-formatted Markdown document containing the latest technology news.',
    agent=markdown_generator
)
Step 4: Setting Up the Crew
To tie all the components together, we need to establish a Crew that orchestrates the workflow from news collection to Markdown generation. This involves linking the agents and their respective tasks to ensure smooth operation and data flow between processes:

from crewai import Crew, Process

# Initialize the Crew with defined agents and tasks
tech_news_crew = Crew(
    name='Daily Tech News Crew',
    agents=[news_collector, content_organizer, markdown_generator],
    tasks=[fetch_news_task, organize_data_task, generate_markdown_task],
    process=Process.sequential,  # Ensures tasks are executed in the order they are added
    verbose=True,
    memory=True,
    cache=True,
    max_rpm=100,
    share_crew=True
)
if __name__ == '__main__':
    # Execute the crew with an input topic for news collection
    result = tech_news_crew.kickoff(inputs={'topic': 'software engineering'})
    print("Crew Execution Result:", result)
This setup allows the crew to operate effectively, handling tasks in a sequential manner to ensure data integrity and coherence. The Process.sequential setting is crucial as it dictates that tasks must be completed in a strict order, aligning with the dependencies each task has on the output of the previous one.

Conclusion
The integration of CrewAI into our daily technology news workflow demonstrates the powerful capabilities of AI-driven automation in content creation and management. By structuring our agents and tasks in a crew, we streamline the process of gathering, organizing, and presenting information. This setup not only enhances productivity but also maintains high standards of data accuracy and presentation quality. As this system evolves, further integration with real-world applications like scheduled updates or real-time news processing could be considered to expand its utility and efficiency.

Reference
YouTube Tutorial: CrewAI Setup and Execution
Official Documentation: CrewAI Documentation
Tool Documentation: SerperDevTool
Complete Project Source Code: GitHub — Daily Tech News Crew