import mysql.connector
connection = mysql.connector.connect(
    host="localhost",
    user="root",
    password="root"
)
cursor = connection.cursor()
cursor.execute("CREATE DATABASE IF NOT EXISTS s")
connection.commit()
cursor.execute("USE s")
create_table_query = """
CREATE TABLE IF NOT EXISTS questions (
    question_id INT AUTO_INCREMENT PRIMARY KEY,
    question_text VARCHAR(255) NOT NULL,
    option1 VARCHAR(255) NOT NULL,
    option2 VARCHAR(255) NOT NULL,
    option3 VARCHAR(255) NOT NULL,
    option4 VARCHAR(255) NOT NULL,
    correct_option VARCHAR(255) NOT NULL
)
"""
cursor.execute(create_table_query)
connection.commit()
cursor.execute("SELECT COUNT(*) FROM questions")
if cursor.fetchone()[0] == 0:
    insert_query = """
    INSERT INTO questions (question_text, option1, option2, option3, option4, correct_option)
    VALUES (%s, %s, %s, %s, %s, %s)
    """
    sample_questions = [
        ('What is the capital of France?', 'Paris', 'Berlin', 'London', 'Madrid', '1'),
        ('What is the largest planet in our solar system?', 'Mars', 'Venus', 'Jupiter', 'Saturn', '3'),
        ('Who painted the Mona Lisa?', 'Vincent van Gogh', 'Pablo Picasso', 'Leonardo da Vinci', 'Michelangelo', '3')
    ]
    cursor.executemany(insert_query, sample_questions)
    connection.commit()
def display_questions():
    cursor.execute("SELECT * FROM questions")
    questions = cursor.fetchall()
    for question in questions:
        print(question[1]) 
        print("1. " + question[2])  
        print("2. " + question[3])  
        print("3. " + question[4]) 
        print("4. " + question[5])  
        print()
def take_quiz():
    cursor.execute("SELECT * FROM questions")
    questions = cursor.fetchall()
    score = 0   
    for question in questions:
        print(question[1])  
        user_answer = input("Enter your answer (1-4): ")
        if user_answer == question[6]:
            score += 1
    return score
print("Welcome to the Quiz Competition!")
cursor.execute("SELECT COUNT(*) FROM questions")
if cursor.fetchone()[0] == 0:
    print("No questions found in the database. Please add questions.")
else:
    print("Here are the questions:\n")
    display_questions()
    print("Please provide your answers:")
    final_score = take_quiz()
    print("Your final score is:", final_score)
connection.close()
