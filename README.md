# Импортруем библиотеку pandas для анализа табличных данных
import pandas as pd
# Напишем функцию которая бы считывала файлы по указанным путям и выдавала 10 лучших фильмов с более чем 3 отзывами
def find_best_movies(ratings_file, movies_file):
    # Считываем данные с файлов
    ratings_data = pd.read_csv(ratings_file)
    movies_data = pd.read_csv(movies_file, on_bad_lines='skip')   # Так как некоторые строки в файле Movies некорректны из-за разделителя - пропустим их

    # Переведем значение movieId в int
    ratings_data['movieId'] = ratings_data['movieId'].astype(int)
    movies_data['movieId'] = movies_data['movieId'].astype(int)

    # Посчитаем средний рейтинг и количество отзывов
    ratings_summary = ratings_data.groupby('movieId').agg({'rating': ['mean', 'count']}).reset_index()
    ratings_summary.columns = ['movieId', 'average_rating', 'rating_count']

    # Отфильтруем таблицу оставив только фильмы с более чем 3 отзывами
    filtered_ratings = ratings_summary[ratings_summary['rating_count'] > 3]

    # Совместим средний рейтинг с названием фильма через movieId
    combined_data = pd.merge(filtered_ratings, movies_data, on='movieId')

    # Отсортрируем по убыванию рейтинга
    sorted_movies = combined_data.sort_values(by='average_rating', ascending=False)

    return sorted_movies

# Указываем путь к файлам
ratings_path = r'C:\Users\ELENA\Downloads\ratings.csv'
movies_path = r'C:\Users\ELENA\Downloads\movies.csv'

# Вызовем функцию для наших файлов
best_movies = find_best_movies(ratings_path, movies_path)

# Выведем на экран 10 лучших фильмов с более чем 3 отзывами
for index, row in best_movies.head(20).iterrows():
    print(f"Название (год): {row['title']}, Средний рейтинг: {row['average_rating']}, Количество отзывов: {row['rating_count']}, Жанр: {row['genres']}")
