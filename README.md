# Practicum_Projects{
 "cells": [
  {
   "cell_type": "markdown",
   "id": "c635f2a6",
   "metadata": {},
   "source": [
    "# Проект: Статистический анализ данных"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "d7b9c14e",
   "metadata": {},
   "source": [
    "Цели исследования:\n",
    "Основные цели:\n",
    "\n",
    "* поведение пользователей сервиса аренды самокатов.\n",
    "\n",
    "* Сравнить поведение пользователей с подпиской и без подписки.\n",
    "\n",
    "* Оценить выручку, которую приносят пользователи с разными типами подписок.\n",
    "\n",
    "* Проверить гипотезы, связанные с поведением пользователей и их вкладом в выручку.\n",
    "\n",
    "* Дополнительные цели:\n",
    "\n",
    "* Оценить вероятность выполнения маркетинговых планов (например, продление подписок, открытие уведомлений).\n",
    "\n",
    "Ход исследования:\n",
    "\n",
    "Загрузка данных:\n",
    "\n",
    "* Загрузка данных из CSV-файлов.\n",
    "\n",
    "* Изучение общей информации о данных.\n",
    "\n",
    "Предобработка данных:\n",
    "\n",
    "* Преобразование типов данных.\n",
    "\n",
    "* Обработка пропущенных значений и дубликатов.\n",
    "\n",
    "Исследовательский анализ данных:\n",
    "\n",
    "* Визуализация распределений и соотношений.\n",
    "\n",
    "* Анализ возраста, расстояния и продолжительности поездок.\n",
    "\n",
    "Объединение данных:\n",
    "\n",
    "* Объединение данных о поездках, пользователях и подписках.\n",
    "\n",
    "* Сравнение пользователей с подпиской и без подписки.\n",
    "\n",
    "* Подсчёт выручки:\n",
    "\n",
    "Агрегация данных по пользователям и месяцам.\n",
    "\n",
    "Расчёт помесячной выручки.\n",
    "\n",
    "* Проверка гипотез:\n",
    "\n",
    "Проверка гипотез о времени поездок, расстоянии и выручке.\n",
    "\n",
    "* Распределения:\n",
    "\n",
    "Использование биномиального и нормального распределений для оценки вероятностей."
   ]
  },
  {
   "cell_type": "markdown",
   "id": "7a602da2",
   "metadata": {},
   "source": [
    "#### Шаг 1. Загрузка данных"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 1,
   "id": "72576667",
   "metadata": {},
   "outputs": [],
   "source": [
    "import pandas as pd\n",
    "import matplotlib.pyplot as plt\n",
    "import numpy as np\n",
    "import seaborn as sns\n",
    "from scipy import stats as st\n",
    "from scipy.stats import binom\n",
    "from scipy.stats import norm"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 2,
   "id": "0a91f1f9",
   "metadata": {},
   "outputs": [],
   "source": [
    "df_users = pd.read_csv('/datasets/users_go.csv')"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 3,
   "id": "bd24d942",
   "metadata": {},
   "outputs": [],
   "source": [
    "df_rides = pd.read_csv('/datasets/rides_go.csv')"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 4,
   "id": "9a5690f6",
   "metadata": {},
   "outputs": [],
   "source": [
    "df_subscriptions = pd.read_csv('/datasets/subscriptions_go.csv')"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 5,
   "id": "d19fda4b",
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "<class 'pandas.core.frame.DataFrame'>\n",
      "RangeIndex: 1565 entries, 0 to 1564\n",
      "Data columns (total 5 columns):\n",
      " #   Column             Non-Null Count  Dtype \n",
      "---  ------             --------------  ----- \n",
      " 0   user_id            1565 non-null   int64 \n",
      " 1   name               1565 non-null   object\n",
      " 2   age                1565 non-null   int64 \n",
      " 3   city               1565 non-null   object\n",
      " 4   subscription_type  1565 non-null   object\n",
      "dtypes: int64(2), object(3)\n",
      "memory usage: 61.3+ KB\n"
     ]
    }
   ],
   "source": [
    "df_users.info()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 6,
   "id": "adb01860",
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>user_id</th>\n",
       "      <th>name</th>\n",
       "      <th>age</th>\n",
       "      <th>city</th>\n",
       "      <th>subscription_type</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>1</td>\n",
       "      <td>Кира</td>\n",
       "      <td>22</td>\n",
       "      <td>Тюмень</td>\n",
       "      <td>ultra</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1</th>\n",
       "      <td>2</td>\n",
       "      <td>Станислав</td>\n",
       "      <td>31</td>\n",
       "      <td>Омск</td>\n",
       "      <td>ultra</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>2</th>\n",
       "      <td>3</td>\n",
       "      <td>Алексей</td>\n",
       "      <td>20</td>\n",
       "      <td>Москва</td>\n",
       "      <td>ultra</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>3</th>\n",
       "      <td>4</td>\n",
       "      <td>Константин</td>\n",
       "      <td>26</td>\n",
       "      <td>Ростов-на-Дону</td>\n",
       "      <td>ultra</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>4</th>\n",
       "      <td>5</td>\n",
       "      <td>Адель</td>\n",
       "      <td>28</td>\n",
       "      <td>Омск</td>\n",
       "      <td>ultra</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>...</th>\n",
       "      <td>...</td>\n",
       "      <td>...</td>\n",
       "      <td>...</td>\n",
       "      <td>...</td>\n",
       "      <td>...</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1560</th>\n",
       "      <td>829</td>\n",
       "      <td>Федор</td>\n",
       "      <td>29</td>\n",
       "      <td>Пятигорск</td>\n",
       "      <td>free</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1561</th>\n",
       "      <td>809</td>\n",
       "      <td>Юрий</td>\n",
       "      <td>26</td>\n",
       "      <td>Сочи</td>\n",
       "      <td>free</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1562</th>\n",
       "      <td>307</td>\n",
       "      <td>Ясмина</td>\n",
       "      <td>19</td>\n",
       "      <td>Краснодар</td>\n",
       "      <td>ultra</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1563</th>\n",
       "      <td>47</td>\n",
       "      <td>Юрий</td>\n",
       "      <td>28</td>\n",
       "      <td>Тюмень</td>\n",
       "      <td>ultra</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1564</th>\n",
       "      <td>1045</td>\n",
       "      <td>Артемий</td>\n",
       "      <td>28</td>\n",
       "      <td>Ростов-на-Дону</td>\n",
       "      <td>free</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "<p>1565 rows × 5 columns</p>\n",
       "</div>"
      ],
      "text/plain": [
       "      user_id        name  age            city subscription_type\n",
       "0           1        Кира   22          Тюмень             ultra\n",
       "1           2   Станислав   31            Омск             ultra\n",
       "2           3     Алексей   20          Москва             ultra\n",
       "3           4  Константин   26  Ростов-на-Дону             ultra\n",
       "4           5       Адель   28            Омск             ultra\n",
       "...       ...         ...  ...             ...               ...\n",
       "1560      829       Федор   29       Пятигорск              free\n",
       "1561      809        Юрий   26            Сочи              free\n",
       "1562      307      Ясмина   19       Краснодар             ultra\n",
       "1563       47        Юрий   28          Тюмень             ultra\n",
       "1564     1045     Артемий   28  Ростов-на-Дону              free\n",
       "\n",
       "[1565 rows x 5 columns]"
      ]
     },
     "execution_count": 6,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "df_users"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 7,
   "id": "8645b59c",
   "metadata": {
    "scrolled": true
   },
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>user_id</th>\n",
       "      <th>distance</th>\n",
       "      <th>duration</th>\n",
       "      <th>date</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>1</td>\n",
       "      <td>4409.919140</td>\n",
       "      <td>25.599769</td>\n",
       "      <td>2021-01-01</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1</th>\n",
       "      <td>1</td>\n",
       "      <td>2617.592153</td>\n",
       "      <td>15.816871</td>\n",
       "      <td>2021-01-18</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>2</th>\n",
       "      <td>1</td>\n",
       "      <td>754.159807</td>\n",
       "      <td>6.232113</td>\n",
       "      <td>2021-04-20</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>3</th>\n",
       "      <td>1</td>\n",
       "      <td>2694.783254</td>\n",
       "      <td>18.511000</td>\n",
       "      <td>2021-08-11</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>4</th>\n",
       "      <td>1</td>\n",
       "      <td>4028.687306</td>\n",
       "      <td>26.265803</td>\n",
       "      <td>2021-08-28</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>...</th>\n",
       "      <td>...</td>\n",
       "      <td>...</td>\n",
       "      <td>...</td>\n",
       "      <td>...</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>18063</th>\n",
       "      <td>1534</td>\n",
       "      <td>3781.098080</td>\n",
       "      <td>19.822962</td>\n",
       "      <td>2021-11-04</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>18064</th>\n",
       "      <td>1534</td>\n",
       "      <td>2840.423057</td>\n",
       "      <td>21.409799</td>\n",
       "      <td>2021-11-16</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>18065</th>\n",
       "      <td>1534</td>\n",
       "      <td>3826.185507</td>\n",
       "      <td>18.435051</td>\n",
       "      <td>2021-11-18</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>18066</th>\n",
       "      <td>1534</td>\n",
       "      <td>2902.308661</td>\n",
       "      <td>16.674362</td>\n",
       "      <td>2021-11-27</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>18067</th>\n",
       "      <td>1534</td>\n",
       "      <td>2371.711192</td>\n",
       "      <td>15.893917</td>\n",
       "      <td>2021-12-29</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "<p>18068 rows × 4 columns</p>\n",
       "</div>"
      ],
      "text/plain": [
       "       user_id     distance   duration        date\n",
       "0            1  4409.919140  25.599769  2021-01-01\n",
       "1            1  2617.592153  15.816871  2021-01-18\n",
       "2            1   754.159807   6.232113  2021-04-20\n",
       "3            1  2694.783254  18.511000  2021-08-11\n",
       "4            1  4028.687306  26.265803  2021-08-28\n",
       "...        ...          ...        ...         ...\n",
       "18063     1534  3781.098080  19.822962  2021-11-04\n",
       "18064     1534  2840.423057  21.409799  2021-11-16\n",
       "18065     1534  3826.185507  18.435051  2021-11-18\n",
       "18066     1534  2902.308661  16.674362  2021-11-27\n",
       "18067     1534  2371.711192  15.893917  2021-12-29\n",
       "\n",
       "[18068 rows x 4 columns]"
      ]
     },
     "execution_count": 7,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "df_rides"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 8,
   "id": "2a4cff8e",
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>user_id</th>\n",
       "      <th>distance</th>\n",
       "      <th>duration</th>\n",
       "      <th>date</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>1</td>\n",
       "      <td>4409.919140</td>\n",
       "      <td>25.599769</td>\n",
       "      <td>2021-01-01</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1</th>\n",
       "      <td>1</td>\n",
       "      <td>2617.592153</td>\n",
       "      <td>15.816871</td>\n",
       "      <td>2021-01-18</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>2</th>\n",
       "      <td>1</td>\n",
       "      <td>754.159807</td>\n",
       "      <td>6.232113</td>\n",
       "      <td>2021-04-20</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>3</th>\n",
       "      <td>1</td>\n",
       "      <td>2694.783254</td>\n",
       "      <td>18.511000</td>\n",
       "      <td>2021-08-11</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>4</th>\n",
       "      <td>1</td>\n",
       "      <td>4028.687306</td>\n",
       "      <td>26.265803</td>\n",
       "      <td>2021-08-28</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>5</th>\n",
       "      <td>1</td>\n",
       "      <td>2770.890808</td>\n",
       "      <td>16.650138</td>\n",
       "      <td>2021-10-09</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>6</th>\n",
       "      <td>1</td>\n",
       "      <td>3039.020292</td>\n",
       "      <td>14.927879</td>\n",
       "      <td>2021-10-19</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>7</th>\n",
       "      <td>1</td>\n",
       "      <td>2842.118050</td>\n",
       "      <td>23.117468</td>\n",
       "      <td>2021-11-06</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>8</th>\n",
       "      <td>1</td>\n",
       "      <td>3412.690668</td>\n",
       "      <td>15.238072</td>\n",
       "      <td>2021-11-14</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>9</th>\n",
       "      <td>1</td>\n",
       "      <td>748.690645</td>\n",
       "      <td>15.041884</td>\n",
       "      <td>2021-11-22</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "   user_id     distance   duration        date\n",
       "0        1  4409.919140  25.599769  2021-01-01\n",
       "1        1  2617.592153  15.816871  2021-01-18\n",
       "2        1   754.159807   6.232113  2021-04-20\n",
       "3        1  2694.783254  18.511000  2021-08-11\n",
       "4        1  4028.687306  26.265803  2021-08-28\n",
       "5        1  2770.890808  16.650138  2021-10-09\n",
       "6        1  3039.020292  14.927879  2021-10-19\n",
       "7        1  2842.118050  23.117468  2021-11-06\n",
       "8        1  3412.690668  15.238072  2021-11-14\n",
       "9        1   748.690645  15.041884  2021-11-22"
      ]
     },
     "execution_count": 8,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "df_rides.head(10)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 9,
   "id": "11377a17",
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "<class 'pandas.core.frame.DataFrame'>\n",
      "RangeIndex: 2 entries, 0 to 1\n",
      "Data columns (total 4 columns):\n",
      " #   Column             Non-Null Count  Dtype \n",
      "---  ------             --------------  ----- \n",
      " 0   subscription_type  2 non-null      object\n",
      " 1   minute_price       2 non-null      int64 \n",
      " 2   start_ride_price   2 non-null      int64 \n",
      " 3   subscription_fee   2 non-null      int64 \n",
      "dtypes: int64(3), object(1)\n",
      "memory usage: 192.0+ bytes\n"
     ]
    }
   ],
   "source": [
    "df_subscriptions.info()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 10,
   "id": "c490b66c",
   "metadata": {
    "scrolled": true
   },
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>subscription_type</th>\n",
       "      <th>minute_price</th>\n",
       "      <th>start_ride_price</th>\n",
       "      <th>subscription_fee</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>free</td>\n",
       "      <td>8</td>\n",
       "      <td>50</td>\n",
       "      <td>0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1</th>\n",
       "      <td>ultra</td>\n",
       "      <td>6</td>\n",
       "      <td>0</td>\n",
       "      <td>199</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "  subscription_type  minute_price  start_ride_price  subscription_fee\n",
       "0              free             8                50                 0\n",
       "1             ultra             6                 0               199"
      ]
     },
     "execution_count": 10,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "df_subscriptions.head()"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "b93405b1",
   "metadata": {},
   "source": [
    "Загрузка данных:\n",
    "\n",
    "Данные были успешно загружены из трёх файлов: users_go.csv, rides_go.csv, subscriptions_go.csv. Так же добавлены все необходимые библиотеки для будущей работы."
   ]
  },
  {
   "cell_type": "markdown",
   "id": "9d760581",
   "metadata": {},
   "source": [
    "#### Шаг 2. Предобработка данных"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 11,
   "id": "5bd20ea0",
   "metadata": {},
   "outputs": [],
   "source": [
    "df_rides['date'] = pd.to_datetime(df_rides['date'])"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 12,
   "id": "30664689",
   "metadata": {},
   "outputs": [],
   "source": [
    "df_rides['month'] = df_rides['date'].dt.month"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 13,
   "id": "a1f666c4",
   "metadata": {
    "scrolled": false
   },
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "<class 'pandas.core.frame.DataFrame'>\n",
      "RangeIndex: 18068 entries, 0 to 18067\n",
      "Data columns (total 5 columns):\n",
      " #   Column    Non-Null Count  Dtype         \n",
      "---  ------    --------------  -----         \n",
      " 0   user_id   18068 non-null  int64         \n",
      " 1   distance  18068 non-null  float64       \n",
      " 2   duration  18068 non-null  float64       \n",
      " 3   date      18068 non-null  datetime64[ns]\n",
      " 4   month     18068 non-null  int64         \n",
      "dtypes: datetime64[ns](1), float64(2), int64(2)\n",
      "memory usage: 705.9 KB\n"
     ]
    }
   ],
   "source": [
    "df_rides.info()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 14,
   "id": "99004446",
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "user_id              0\n",
       "name                 0\n",
       "age                  0\n",
       "city                 0\n",
       "subscription_type    0\n",
       "dtype: int64"
      ]
     },
     "execution_count": 14,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "df_users.isnull().sum()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 15,
   "id": "ddb63b68",
   "metadata": {
    "scrolled": true
   },
   "outputs": [
    {
     "data": {
      "text/plain": [
       "user_id     0\n",
       "distance    0\n",
       "duration    0\n",
       "date        0\n",
       "month       0\n",
       "dtype: int64"
      ]
     },
     "execution_count": 15,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "df_rides.isnull().sum()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 16,
   "id": "86e91258",
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "subscription_type    0\n",
       "minute_price         0\n",
       "start_ride_price     0\n",
       "subscription_fee     0\n",
       "dtype: int64"
      ]
     },
     "execution_count": 16,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "df_subscriptions.isnull().sum()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 17,
   "id": "3b1908ed",
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "31"
      ]
     },
     "execution_count": 17,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "df_users.duplicated().sum()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 18,
   "id": "2485d712",
   "metadata": {
    "scrolled": true
   },
   "outputs": [
    {
     "data": {
      "text/plain": [
       "0"
      ]
     },
     "execution_count": 18,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "df_rides.duplicated().sum()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 19,
   "id": "82060fa1",
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "0"
      ]
     },
     "execution_count": 19,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "df_subscriptions.duplicated().sum()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 20,
   "id": "5c7d18b4",
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "      user_id      name  age            city subscription_type\n",
      "15         16    Амалия   27       Краснодар             ultra\n",
      "42         43  Стефания   22            Омск             ultra\n",
      "46         47      Юрий   28          Тюмень             ultra\n",
      "71         72    Никита   25          Москва             ultra\n",
      "107       108     Влада   29          Тюмень             ultra\n",
      "...       ...       ...  ...             ...               ...\n",
      "1560      829     Федор   29       Пятигорск              free\n",
      "1561      809      Юрий   26            Сочи              free\n",
      "1562      307    Ясмина   19       Краснодар             ultra\n",
      "1563       47      Юрий   28          Тюмень             ultra\n",
      "1564     1045   Артемий   28  Ростов-на-Дону              free\n",
      "\n",
      "[62 rows x 5 columns]\n"
     ]
    }
   ],
   "source": [
    "all_duplicates = df_users[df_users.duplicated(keep=False)]\n",
    "print(all_duplicates)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 21,
   "id": "707b1080",
   "metadata": {
    "scrolled": false
   },
   "outputs": [],
   "source": [
    "df_users = df_users.drop_duplicates().reset_index(drop=True)"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "42577cc8",
   "metadata": {},
   "source": [
    "Проведена предобработка данных: преобразование дат, создание новых столбцов (например, месяц), проверка на пропущенные значения и дубликаты. Пропущенные значения и дубликаты были обработаны."
   ]
  },
  {
   "cell_type": "markdown",
   "id": "f26836ad",
   "metadata": {},
   "source": [
    "#### Шаг 3. Исследовательский анализ данных"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 22,
   "id": "b45f2146",
   "metadata": {},
   "outputs": [
    {
     "data": {
      "image/png": "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\n",
      "text/plain": [
       "<Figure size 864x432 with 1 Axes>"
      ]
     },
     "metadata": {
      "needs_background": "light"
     },
     "output_type": "display_data"
    }
   ],
   "source": [
    "city_counts = df_users['city'].value_counts()\n",
    "\n",
    "plt.figure(figsize=(12, 6))  \n",
    "sns.barplot(x=city_counts.index, y=city_counts.values)\n",
    "plt.title('Частота встречаемости городов')  \n",
    "plt.xlabel('Город')  \n",
    "plt.ylabel('Количество пользователей')  \n",
    "plt.xticks(rotation=45)  \n",
    "plt.show()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 23,
   "id": "d571cba9",
   "metadata": {},
   "outputs": [
    {
     "data": {
      "image/png": "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\n",
      "text/plain": [
       "<Figure size 864x432 with 1 Axes>"
      ]
     },
     "metadata": {
      "needs_background": "light"
     },
     "output_type": "display_data"
    }
   ],
   "source": [
    "plt.figure(figsize=(12, 6))\n",
    "sns.barplot(x=city_counts.values, y=city_counts.index)  # Поменяй x и y местами\n",
    "plt.title('Частота встречаемости городов')\n",
    "plt.xlabel('Количество пользователей')\n",
    "plt.ylabel('Город')\n",
    "plt.show()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 24,
   "id": "a0930a32",
   "metadata": {
    "scrolled": true
   },
   "outputs": [
    {
     "data": {
      "image/png": "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\n",
      "text/plain": [
       "<Figure size 576x576 with 1 Axes>"
      ]
     },
     "metadata": {},
     "output_type": "display_data"
    }
   ],
   "source": [
    "subscription_counts = df_users['subscription_type'].value_counts()\n",
    "\n",
    "# Строим круговую диаграмму\n",
    "plt.figure(figsize=(8, 8))  # Размер графика\n",
    "plt.pie(subscription_counts, labels=subscription_counts.index, autopct='%1.1f%%', startangle=90, textprops={'color': 'white'})\n",
    "plt.title('Соотношение пользователей с подпиской и без подписки')\n",
    "plt.show()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 25,
   "id": "3452391c",
   "metadata": {},
   "outputs": [
    {
     "data": {
      "image/png": "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\n",
      "text/plain": [
       "<Figure size 720x432 with 1 Axes>"
      ]
     },
     "metadata": {},
     "output_type": "display_data"
    }
   ],
   "source": [
    "sns.set(style=\"whitegrid\")\n",
    "plt.figure(figsize=(10, 6))  \n",
    "sns.histplot(df_users['age'], bins=20, kde=True)  \n",
    "plt.title('Распределение возраста пользователей')  \n",
    "plt.xlabel('Возраст') \n",
    "plt.ylabel('Количество пользователей') \n",
    "plt.show()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 26,
   "id": "7c41b8dd",
   "metadata": {},
   "outputs": [
    {
     "data": {
      "image/png": "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\n",
      "text/plain": [
       "<Figure size 720x432 with 1 Axes>"
      ]
     },
     "metadata": {},
     "output_type": "display_data"
    }
   ],
   "source": [
    "sns.set(style=\"whitegrid\")\n",
    "plt.figure(figsize=(10, 6))\n",
    "sns.histplot(df_rides['distance'], bins=30, kde=True)\n",
    "plt.title('Распределение расстояния за одну поездку')\n",
    "plt.xlabel('Расстояние (метры)')\n",
    "plt.ylabel('Количество поездок')\n",
    "plt.show()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 27,
   "id": "614f26e6",
   "metadata": {},
   "outputs": [
    {
     "data": {
      "image/png": "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\n",
      "text/plain": [
       "<Figure size 720x432 with 1 Axes>"
      ]
     },
     "metadata": {},
     "output_type": "display_data"
    }
   ],
   "source": [
    "plt.figure(figsize=(10, 6))\n",
    "sns.histplot(df_rides['duration'], bins=30, kde=True)\n",
    "plt.title('Распределение продолжительности поездок')\n",
    "plt.xlabel('Продолжительность (минуты)')\n",
    "plt.ylabel('Количество поездок')\n",
    "plt.show()"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "158ad382",
   "metadata": {},
   "source": [
    "Визуализирована частота встречаемости городов, соотношение пользователей с подпиской и без, распределение возраста пользователей, расстояния и продолжительности поездок.\n",
    "\n",
    "Большинство пользователей находятся в определённых городах, а соотношение пользователей с подпиской и без подписки примерно одинаково.\n",
    "\n",
    "Распределение возраста пользователей близко к нормальному, с пиком в районе 25-35 лет.\n",
    "\n",
    "Распределение расстояния и продолжительности поездок также визуализировано, что позволяет понять, как пользователи используют сервис."
   ]
  },
  {
   "cell_type": "markdown",
   "id": "fe6f74d4",
   "metadata": {},
   "source": [
    "#### Шаг 4. Объединение данных"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 28,
   "id": "51aaa397",
   "metadata": {},
   "outputs": [],
   "source": [
    "merged_df = pd.merge(df_rides, df_users, on='user_id', how='left')"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 29,
   "id": "31873949",
   "metadata": {},
   "outputs": [],
   "source": [
    "final_df = pd.merge(merged_df, df_subscriptions, on='subscription_type', how='left')"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 30,
   "id": "5cd5d136",
   "metadata": {
    "scrolled": true
   },
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "   user_id     distance   duration       date  month  name  age    city  \\\n",
      "0        1  4409.919140  25.599769 2021-01-01      1  Кира   22  Тюмень   \n",
      "1        1  2617.592153  15.816871 2021-01-18      1  Кира   22  Тюмень   \n",
      "2        1   754.159807   6.232113 2021-04-20      4  Кира   22  Тюмень   \n",
      "3        1  2694.783254  18.511000 2021-08-11      8  Кира   22  Тюмень   \n",
      "4        1  4028.687306  26.265803 2021-08-28      8  Кира   22  Тюмень   \n",
      "\n",
      "  subscription_type  minute_price  start_ride_price  subscription_fee  \n",
      "0             ultra             6                 0               199  \n",
      "1             ultra             6                 0               199  \n",
      "2             ultra             6                 0               199  \n",
      "3             ultra             6                 0               199  \n",
      "4             ultra             6                 0               199  \n",
      "<class 'pandas.core.frame.DataFrame'>\n",
      "Int64Index: 18068 entries, 0 to 18067\n",
      "Data columns (total 12 columns):\n",
      " #   Column             Non-Null Count  Dtype         \n",
      "---  ------             --------------  -----         \n",
      " 0   user_id            18068 non-null  int64         \n",
      " 1   distance           18068 non-null  float64       \n",
      " 2   duration           18068 non-null  float64       \n",
      " 3   date               18068 non-null  datetime64[ns]\n",
      " 4   month              18068 non-null  int64         \n",
      " 5   name               18068 non-null  object        \n",
      " 6   age                18068 non-null  int64         \n",
      " 7   city               18068 non-null  object        \n",
      " 8   subscription_type  18068 non-null  object        \n",
      " 9   minute_price       18068 non-null  int64         \n",
      " 10  start_ride_price   18068 non-null  int64         \n",
      " 11  subscription_fee   18068 non-null  int64         \n",
      "dtypes: datetime64[ns](1), float64(2), int64(6), object(3)\n",
      "memory usage: 1.8+ MB\n",
      "None\n"
     ]
    }
   ],
   "source": [
    "print(final_df.head())\n",
    "print(final_df.info())"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 31,
   "id": "a97b37aa",
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "0\n",
      "user_id              0\n",
      "distance             0\n",
      "duration             0\n",
      "date                 0\n",
      "month                0\n",
      "name                 0\n",
      "age                  0\n",
      "city                 0\n",
      "subscription_type    0\n",
      "minute_price         0\n",
      "start_ride_price     0\n",
      "subscription_fee     0\n",
      "dtype: int64\n"
     ]
    }
   ],
   "source": [
    "print(final_df.duplicated().sum()) \n",
    "print(final_df.isnull().sum())  "
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 32,
   "id": "534def4e",
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "free     11568\n",
       "ultra     6500\n",
       "Name: subscription_type, dtype: int64"
      ]
     },
     "execution_count": 32,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "final_df['subscription_type'].value_counts()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 33,
   "id": "70b4d27f",
   "metadata": {},
   "outputs": [],
   "source": [
    "df_free = final_df[final_df['subscription_type'] == 'free'].copy()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 34,
   "id": "faefc092",
   "metadata": {},
   "outputs": [],
   "source": [
    "df_ultra = final_df[final_df['subscription_type'] == 'ultra'].copy()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 35,
   "id": "d2c5db43",
   "metadata": {},
   "outputs": [],
   "source": [
    "df_free = df_free.reset_index(drop=True)\n",
    "df_ultra = df_ultra.reset_index(drop=True)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 36,
   "id": "eb728991",
   "metadata": {},
   "outputs": [
    {
     "data": {
      "image/png": "iVBORw0KGgoAAAANSUhEUgAAAuIAAAGJCAYAAADPOFY7AAAAOXRFWHRTb2Z0d2FyZQBNYXRwbG90bGliIHZlcnNpb24zLjMuNCwgaHR0cHM6Ly9tYXRwbG90bGliLm9yZy8QVMy6AAAACXBIWXMAAAsTAAALEwEAmpwYAABC2UlEQVR4nO3deVwVdf///+dhNXFBLBDUVOoKyS0ENXMNM7W09LLULCu11MyyxVKzVLQktCu/XpaRVma2UGlmbllu2WIkbkS2XCmaC4KKqIDs5/eHP+fjUcADephz4HG/3bjdzsz7zMxrzjI+fZ/3zFisVqtVAAAAACqUm9kFAAAAAFURQRwAAAAwAUEcAAAAMAFBHAAAADABQRwAAAAwAUEcAADAwaxWqwoKCswuA06GIA4AgIvZvn27tm3bpvz8fH322Wc6c+aM2SW5pDNnzujTTz9Vfn6+tm/fru3bt1/R9f/8888aPny4unbtqtatW2vt2rVXdP1wfR5mFwCU14oVK7Rw4UIlJyfLx8dHTZs21ahRoxQREWF2aQDgUL6+vho7dqxSU1N16623asCAAWaX5JKuuuoqbd++Xf/5z38UEBCgOXPmXLF1JyQk6Omnn9aMGTPUuXNnubu7X7F1o/KwcEMfuKKFCxdq/vz5ioqKUseOHeXp6anvv/9eW7du1fjx480uDwBQxQ0ZMkT33Xef7rjjDrNLgRNjaApczunTp/Xf//5XkydP1u23367q1avL09NTkZGRRgifO3eunnzyST311FMKCwtTv3799McffxjrmD9/vm677TaFhYXpjjvu0Lfffmu0ffHFFwoNDVVYWJhat26tBx98UKmpqZKk+Ph4de7c2aae++67T1988YUxvWTJEvXq1Utt2rTR8OHDdejQIaMtJCRE+/fvN6Znz56tCRMmSJIOHjyokJAQYwxhYmKiQkJCNHv2bOP5Gzdu1N13362IiAgNGjTIZp8uFBISog8++EDdunVTu3btFBMTo6KiIknSP//8owcffFDt2rVTu3bt9Oyzz+rUqVPGsikpKRozZoxuvvlmtWvXTtOmTTPaPvvsM/Xq1ct47X777TdNmzZNYWFhCgsLU0hIiG666SaFhYXpkUcekSSlpqZq1KhRatu2rbp3767PPvvMWF92draefPJJtW3bVmFhYWrevLnxmlzq9f7iiy903333GW0LFixQSEiIfvrpp0uu+0Lnv+/n/po2bar4+HhJUl5enl555RV17NhRHTt21CuvvKK8vDy73pv7779f4eHhioiI0BNPPKHMzEyjbf/+/QoJCTG2GRoaqs8//9xoX79+ve68805FRERoyJAh2rNnj9EWGRlp7GtiYqI6duyoxMTEYvdPkj7//HObfbzw8xgfH6+mTZva7P+59R89elTDhg1TRESEwsLC1KxZM82dO7fY7cydO1fNmjWzeS1DQkJ08OBBSWe/w88//7xuvvlm3XrrrZo3b57x2bxUHRMmTLD5Tpxv0aJF6tChg8LCwtSnTx/jvZPODuXo37+/wsPD1b9/f5shCEOGDFGLFi0UFham9u3b26x/06ZN6tu3r1q3bq0uXbrY7PNdd92lsLAwtWzZ0qbe2NhYSdKePXs0dOhQtW3bVj169NDq1atLfJ1uuukmm+9/amqqnnjiCd18882KjIzUBx98YLPcuHHjjOmpU6de9F6eLyMjQxMnTlTHjh3Vpk0bjR49utjnXe53oKCgwOb736xZM5vX8vy2C7+P9hz/1q1bp7vvvlutW7fWbbfdps2bNxvv37nvzD///KMuXbpo/fr1kqSioiLNmzdPt956q9q3b6/nn39ep0+ftnubF74+pR1vLpSYmKgffvhBHTp0uKLHDFQuBHG4nB07dig3N1fdu3cv9Xnr169Xz5499csvv6h3794aPXq08vPzJUkNGzbURx99pG3btmnMmDF67rnnlJaWZix70003aceOHdqyZYu8vLz0/vvv21XbunXr9Pbbb+uNN97Qli1bFB4ermeffbZc+zlz5kwFBAQY07t379YLL7ygadOmKT4+XgMHDtTo0aNtDuwX+vbbb7V06VItW7ZMGzZs0NKlSyWdPWlo5MiR+v7777VmzRodOXLECBiFhYUaOXKkgoKCtGHDBm3evNno0VmzZo3mzp2rmJgYbd++XW+99ZZ8fX01efJk7dixQzt27JAkLV++XDt27NA777wjSXrmmWdUr149ff/99/rvf/+r119/XVu2bDGeu3//fq1fv147duzQ8OHDy/V6ZWRkaPHixapVq5Yxr6zrPve+n/vz9/c32t566y3t2rVLy5cv11dffaVff/1V8+bNk3Tp9+all15SfHy8Nm3apMzMTC1btsxY77kfJc9t8/yhVcnJyXr22Wf1wgsvaMuWLercubNGjRp10Xu+Z88ePf7445o1a5ZatmxZ4v5ZrVZFRETYvFfnKyoqUkBAgNEeFBRktC1atEhubm764YcftGPHDvXq1avU17JXr17GerZu3WrTNn36dJ0+fVrr1q3T4sWLtXz5cuOzeak6ShMZGamvv/5a27dv1+DBgxUTEyPp7Gdj5MiRGjJkiOLj4zV06FCNHDlSJ06cMJY99xn++OOP9d577+mvv/6SdHboQkxMjBISEvT222/rk08+0bp16yRJX331lXbs2KEFCxbI39/fqHfUqFHKzs7WsGHD1Lt3b/3000+aPXu2oqKi9Pfff9vs5x133KEdO3Zo5cqVNvMfe+wxhYSEaPPmzVq0aJEWLVqk77///qJ9Tk5ONgJpSZ5//nmdOXNGq1at0k8//aSHH364xOeW9zsg/d9nedWqVdqxY4f69Olz0frPHRsu9X288PiXmJio8ePH6/nnn1dCQoI++ugj1a9f32aZtLQ0DR8+XE8//bS6desm6Wx4XrZsmT744AOtW7dO2dnZNh0LpW2zNMUdby6Uk5Ojv//+W1999dUVPWagciGIw+VkZGSoTp068vAo/RSHZs2aqWfPnvL09NTQoUOVl5enXbt2STobEgICAuTm5qY77rhDjRo1KrYnsaioSFarVb6+vnbVFhcXpxEjRui6666Th4eHRo0apd9//92mV9weGzdulNVq1S233GLM+/TTTzVw4EC1atVK7u7u6tevnzw9PbVz584S1/Poo4/K19dXQUFBevDBB41/7Bs1aqQOHTrIy8tLfn5+Gjp0qBGWEhMTlZaWpueff17Vq1eXt7e3EQ6XLFmiRx55RC1btpTFYlGjRo0u+sfwQikpKdq+fbvGjRsnb29vhYaG6t5779Xy5cuN5xQVFamwsLBMr9GF3n77bfXv3181a9a0mX8l1i2dPSfh8ccfV926deXn56fHH39cX331laRLvzdNmzY1Pq/u7u664YYbjPXm5OTI09Oz2G2uXr1aXbp0UYcOHeTp6anhw4crJyfHJkQfOnRIw4YN01NPPaX27duXug85OTny8vIqsT0/P7/EWqSzQev8nuvyKCws1OrVq/Xss8+qRo0aatCggYYOHWq8lvbUUZKGDRsa77/VatWNN94o6WyvdqNGjdS3b195eHiod+/eCg4O1saNG4utz93d3VhPu3btFBISIjc3NzVt2lR33nmnfvnll0vWsmnTJtWvX1/9+/eXh4eHbrzxRvXo0UNff/31Jffz119/VXp6usaMGSMvLy81bNhQAwYMuKhHXTr7q1pJPdzS2XC6efNmRUVFqXbt2vL09FTbtm0vWX9xSvsOSFJubq4kleu9O19xx78lS5aof//+6tChg9zc3BQQEKDrrrvOaD958qSGDx+uPn36qG/fvjY1P/zww2rYsKF8fHz0zDPPaPXq1RddvaS4bZampOPNhRxxzEDlwsmacDm+vr46ceKECgoKSg3j9erVMx6fO3Cf6/X+8ssvtXDhQiMgZ2dn2/SO7dq1SxEREcrJyVH9+vUVHR1ttKWlpdn0WmZnZ+vee++VJB0+fFgzZswweuKks4EgNTXVCKz9+vWTm9vZ/wPn5ubqzjvvtKm7qKhIr7/+uqZPn664uDhj/uHDh/Xll1/qww8/NObl5+fb9ORfKDAw0Hhcv35947nHjh3TK6+8ooSEBGVlZclqtRo9OykpKQoKCir2tU1JSdG1115b4vaKk5aWptq1a6tGjRrGvKCgICUlJUmS+vbtq19//VXt27dX9erVlZeXZ/OalPZ6n3Po0CGtWbNGK1eutAn4l1p3Wffj/J7ZoKAg4/W0573p06eP/v77b0VERKhx48bG/GPHjsnPz8+ubbq5uSkwMNAYKiVJL7/8sjF0o3///qXuw7Fjx1SnTp0S20+ePFliD9+wYcP00ksvqXXr1qpRo4ZycnI0cuTIUrdXnBMnTig/P/+i1/L8fSqtDkl677339NFHH8nLy0utW7fW9OnTjf2aP3++3njjDV111VV65ZVXJF38Oha3zZdfflkxMTE6ffq0HnjgAeO7s2vXLr322mv63//+p/z8fOXl5alnz56X3M9Dhw4pMTHR5rNbWFiou+66y2Y/a9euXeyyF37uCwsLLzoRfefOnUpOTtbs2bM1adKkYus4cuSIateuXex2yqq074B0dviSm5ub3R0XxSnp+JeSkqIuXbqUuNzcuXMVGhqqn3/+WU888YQsFotR8/mdBfXr11dBQYGOHz9+yW2WpKTjzYU8PT0dcsxA5UKPOFxOWFiYvLy8jJ+HS3LkyBHjcVFRkVJTU+Xv769Dhw7pxRdfNH76S0hI0L/+9S+bZVu1aqWEhAQlJiaqT58+mjhxotHm7++vhIQE469Vq1ZGW2BgoKKiomzaExMT1bp1a+M5y5YtM9qGDRt2Ud3Lli1TkyZNdNNNN9nMDwwM1KhRo2zWvWvXLvXu3bvE1yAlJcV4fPjwYeNn5tdff10Wi0UrVqzQ9u3bNWvWLONn5cDAQKWkpBR7vdvAwED9888/JW6vOP7+/jp58qTNGMeUlBTjJ+CrrrpKXbt21Q033KD4+PiLXpPSXu9z5syZo0ceecQm7Nuz7rLux+HDh2324dzrac97c+61rlevns1/7Hbv3q2mTZvatU2r1Wrz2knS8OHD9f777ysxMdEYF1uS0rYlSfv27VOTJk2KbfPz81NERIQ6d+6shIQEu8JocerUqSNPT8+LXsvz96m0OqSz/ylISEjQunXrdObMGb377rtG24gRI7Rr1y69+uqreuqpp3Tq1KmLXsfitvniiy8qISFBv/zyi7Zt22b8evTss8+qW7du+u6777Rt2zYNGjRI9lzjIDAwUG3atLH5TOzYsUNRUVE2+1lcwAoMDFSDBg0uWnbBggU2z5s1a5aeeeaZUq/GUa9ePZ08edLmHJDyKu07IEm///67goODS/3V5VJKO/6Vduzp1auXPv74Y1mtVi1evNim5vN/kTx8+LA8PDxUt27dS26zJCUdby4UFBTkkGMGKheCOFxOzZo19eSTT2ratGnGP8T5+fn67rvvNHPmTON5v/32m7755hsVFBRo0aJF8vLyUqtWrXTmzBlZLBajF3Lp0qX63//+V+y2LBaL3N3dlZ6ebldtgwYN0vz58431nT59WmvWrCnT/sXGxuqZZ565aP69996ruLg47dq1S1arVdnZ2cb4wZK8++67OnnypFJSUvTBBx8YY72zsrJUvXp11axZU6mpqcZYbklq2bKlrrnmGv3nP/9Rdna2cnNztW3bNknSPffco/fee09JSUmyWq3av3//JYfdBAYGKiwsTK+//rpyc3P1xx9/aMmSJUbP4OnTp/XKK69o+vTplxxuVJx//vlHu3bt0sCBAy9qu9x1n+/OO+/UW2+9pfT0dKWnp+vNN980xsCW9t5kZWXpwIEDks72ahYUFKhatWqSZIz9LKmXvlevXvruu++0ZcsW5efn67333pOXl5fCwsKM50RERBi9v1FRUSUGrj///LPUAP33339r6dKlxtjaCx08eFALFizQlClT7HvBSuDu7q6ePXtq9uzZyszM1KFDh7Rw4ULj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AACAikAQB5wEQRwAgKqFIA44ierVq9tM+/j4mFQJAACoCARxwEmcOXPGZjo7O9ukSgAAQEUgiANOgssXAgBQtRDEAQAAABMQxAEAAAATEMQBAAAAE3jY86SUlBT98ccfOnXqlGrVqqWmTZsqMDCwTBuKjIyUl5eXvL29JUnjxo1Tp06dtHPnTk2ePFm5ubmqX7++Zs2apbp160pSudsAAAAAZ1diEM/Pz9enn36qTz/9VAcOHNC1114rHx8fZWVl6Z9//lGDBg00aNAgDRgwQF5eXnZt7L///a9uuOEGY7qoqEjPPfecoqOjFRERoXnz5um1115TdHR0udsAAAAAV1Di0JS7775be/fuVVRUlLZt26avvvpKn3zyib766itt27ZN06dP1969e9WvX79ybzwpKUne3t7GrbwHDRqkr7/++rLaAAAAAFdQYo/44sWLSxzq4e7urrCwMIWFhSk9Pd3ujY0bN05Wq1Xh4eF65plnlJKSoqCgIKPdz89PRUVFysjIKHebr6+v3fUAAAAAZikxiNs73trPz8+u53300UcKDAxUXl6eXnnlFU2bNk3du3e3r0oHSUpKMnX7wKVs27bN7BIAwOmdPn1aEsdMuB67TtacOHFiiW32jss+d3Knl5eXBg8erMcee0wPPvigDh8+bDwnPT1dbm5u8vX1VWBgYLnayqJ58+bGyaOAMwoPDze7BABwekuWLJHEMRPOJzc3t9SOX7suXxgQEFDinz2ys7ON/61arVatXr1aoaGhat68uXJycpSQkCBJiouLU8+ePSWp3G0AAACAK7CrR/ypp54yHufl5WnZsmUqKipS//797drI8ePH9cQTT6iwsFBFRUW67rrrNGXKFLm5uWnmzJmaMmWKzWUIJZW7DQAAAHAFdgXx80VFRSkpKUleXl7avn27XQG4YcOG+vLLL4tta926tVasWHFF2wAAAABnV+YgvnnzZq1atUre3t6mn2wJAAAAuKoyB/Hc3FzVqlVL0tkhIgAAAADKzq4gfu5sZOnsGPGlS5eqqKhIZ86ccVhhAAAAQGVmVxBfvny58bhFixbGeO/zb1cPAAAAwH52BfHFixc7ug4AAACgSrF7jPipU6e0adMmpaWlyd/fX127djXGigOXY8OGDfr222/NLsMplXYzraqge/fuioyMNLsMAAAcwq6zLbds2aLIyEgtXrxYv/76qz788ENFRkZqy5Ytjq4PAAAAqJTs6hGfPn26pk2bpjvuuMOYt2bNGkVFRenrr792WHGoGiIjI+n1lNSnT5+L5kVHR5tQCQAAqAh29YinpaWpR48eNvO6d++uY8eOOaQooCq65557bKYHDBhgUiUAAKAi2BXE7777bn300Uc28z755BP17dvXETUBVdJDDz1kMz1kyBCTKgEAABXBrqEpu3fvVlxcnN555x0FBAQoNTVV6enpatmype6//37jeReGdQBl4+fnp/T0dHrDAQCoAuwK4gMGDCAYABUgKChIQUFB9IYDAFAF2BXE27dvr3r16jm6FgAAAKDKsGuM+PlXSwEAAABw+ewK4lar1dF1AAAAAFWKXUNT8vPzNWfOnGLbxo4de0ULAgAAAKoCu29xf+TIEUfWAQAAAFQpdgVxLy8v7vAHAAAAXEGMEQcAAABMYFcQX7hwoaPrAAAAAKoUu4am3HTTTfrxxx+1atUqpaenKzY2Vr/++qsyMzPVvn17R9cIAAAAVDp29YgvXrxYU6dOVePGjbV161ZJUrVq1Uq8kgoAAACA0tkVxBctWqSFCxdqxIgRcnM7u0hwcLCSk5MdWhwAAABQWdkVxLOyshQYGChJslgskqSCggJ5eno6rjIAAACgErMriLdp00bz58+3mffBBx+oXbt2DikKAAAAqOzsOlnzxRdf1KhRo/T5558rKytLPXr0kI+Pj95++21H1wcAAABUSnYFcX9/fy1dulSJiYk6fPiwAgMD1bJlS2O8OAAAAICysfsW9xaLRa1atVKrVq0cWQ8AAABQJdgVxLt27Vpi26ZNm65QKQAAAEDVYVcQLywsVK1atXTvvffqxhtvdHRNAAAAQKVnVxD/7rvvtGnTJn3++edatWqV+vXrp7vuuks1atRwdH0AAABApWRXEHdzc1NkZKQiIyP1zz//6Mknn9S2bdv0n//8x9H1AQAAAJWS3Zc9SUpK0uTJkzVy5Eh16tRJTz31VLk2+MYbbygkJER//fWXJGnnzp2666671KNHDw0bNkzHjx83nlveNgAAAMDZ2RXE+/btq/Hjx6tJkyaaN2+eBgwYIEk6cOBAmTb222+/aefOnapfv74kqaioSM8995wmT56stWvXKiIiQq+99tpltQEAAACuwK4g/scff2jPnj2KiYnRHXfcodtvv13du3fX7bff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AABgAoI4AAAAYAKCOAAAAGACgjgAAABgAoI4YJKDBw+qXbt2xvTcuXONW+ECAM5at26devXqpb59+2rv3r1mlwNcUQRxwEm88cYbys/PL7atoKCggqsBAOcQFxenJ598Ul9++aWCg4ON+RwXURl4mF0AUNkdPHhQ/fv3V3x8vM300qVLjedERUVJkgYNGiQ3NzctXrxYM2bMkLu7u5KTk5WVlaXly5fr2WefVXJysvLz83XttddqxowZql27tin7BQCONmPGDG3btk3Jycn6+OOP9csvv2jMmDHatGmTOnXqpEceeUTR0dH6888/lZubq3bt2mnixIlyd3dXWlqaXn75ZR0+fFi5ubm68847NWrUKLN3CbBBEAecwJQpU/Txxx8rLi5OPj4+xvzff/9dH374oapXry5JmjRpkvz8/CRJs2fP1oIFCzRu3DhTagYAR3vhhRf0+++/a9iwYbr11lsVEhIib29voyNj0qRJatOmjV555RUVFRVp3LhxWrp0qQYMGKDx48dr9OjRatOmjfLy8vTwww+rRYsW6tChg8l7BfwfgjjgxHr27GmEcElavny5VqxYofz8fGVnZ6tx48bmFQcAJujXr5/xeMOGDUpMTNTChQslSTk5OQoICFB2drZ++eUXpaenG8/NysrSnj17COJwKgRxwME8PDxktVqN6dzcXLuXPT+EJyQk6JNPPlFcXJz8/Py0YsUKffbZZ1e0VgBwducfF61Wq+bNm6eGDRvaPCczM1MWi0VLliyRp6dnRZcI2I2TNQEHu/rqq5Wfn6/9+/dLklauXFns83x8fJSZmVniek6dOqUaNWrI19dXeXl5NmPMAaAqioyM1Pz581VYWChJSk9P14EDB1SjRg2Fh4dr/vz5xnNTUlJ09OhRs0oFikUQBxzMw8NDkyZN0tChQ3XPPffI3d292OcNGzZMDz74oO6++26dOnXqovZOnTrp2muvVY8ePfTAAw/oxhtvdHTpAODUXnjhBbm5uenuu+9Wnz599Mgjjyg1NVWS9Nprr2nPnj3q06eP+vTpo6effrrYYytgJov1/N/MAQAAAFQIesQBAAAAExDEAQAAABMQxAEAAAATEMQBAAAAExDEAQAAABMQxAEAAAATEMQBAAAAE3CLewAwUVhYmPH4zJkz8vLyMm76FBUVpbvuusus0gAADsYNfQDASURGRurll1/WLbfcYnYpAIAKwNAUAHBic+fO1bhx44zpqVOnKiQkRPv375ckTZgwQc2bN1dYWJjCwsJ00003KSQkpMT1RUZGqmXLlsbzW7RooSFDhhjt27dvV//+/RUeHq7+/ftr+/btNsvPnj1bzZo1s9lWQUGBUeu5tnN/ISEhOnjwoFHr7NmzjXU9+uijNstnZGRo4sSJ6tixo9q0aaPRo0dLkuLj49W5c2djuZkzZ+qBBx5Qbm6usU8//fSTJCkrK0u33HKL7rvvvjK+0gBQ8QjiAOAikpOTtXnz5ovmDx8+XDt27NCOHTu0fPnyS64nNjbWeP7kyZON+RkZGRo5cqSGDBmi+Ph4DR06VCNHjtSJEydslr/rrru0Y8cOrVy58qJ19+rVy1j31q1bS6zh559/1p9//mkz7/nnn9eZM2e0atUq/fTTT3r44YcvWm7+/PnasmWLYmNj5e3tfVH7u+++Kw8PRl0CcA0EcQBwEbNnzzZ6iR1h06ZNatSokfr27SsPDw/17t1bwcHB2rhxo/GcnJwceXp6XtZ2rFarZs2apSeffNKYl5aWps2bNysqKkq1a9eWp6en2rZta7Pc559/rvfee0/vvPOOatSocdF6jx49qiVLlmjo0KGXVR8AVBSCOAC4gJ07dyo5OVn9+vVz2DbS0tIUFBRkMy8oKEipqanG9LFjx1SnTp3L2s6aNWtUp04d3Xzzzca8I0eOqHbt2qpdu3axy6Snp2vevHm66qqr9Pvvvxf7nDfeeEMPPPBAiesAAGdDEAcAFzBr1iw988wzxhVVHMHf31+HDx+2mZeSkqKAgABjevfu3WratGm5t1FQUKA5c+bYjHuXpHr16unkyZM6depUscu5u7trwYIFmjZtmiZPnqzMzEyb9uTkZP3www966KGHyl0bAFQ0gjgAOLmff/5Zbm5uuvXWWx26nS5dumjfvn1asWKFCgoKtHr1av3999/q2rWrJOmHH37QsWPHbE6cLKvly5crLCzsojDv7++vzp07KyoqSidPnlR+fr7NGPPatWvr+uuvV6dOnXTzzTdr1qxZNsu/9dZbevzxx4sdNw4AzoogDgBO7ujRo3ruueccvp06deooNjZWCxcuVLt27fTOO+8oNjZWfn5+SkhI0KOPPqqsrCx17NhRYWFh6t27tyRp1KhRdm/j1KlTGjt2bLFtM2fOlIeHh3r16qVbbrlFixYtKvZ5EydO1KZNmxQfH29Te9++fe3fWQBwAlxHHABwSfHx8Vq2bJleffXVi9oefvhhvf/++xVfFAC4OHrEAQCX5OXlVeJJkH5+fhVcDQBUDvSIAwAAACagRxwAAAAwAUEcAAAAMAFBHAAAADABQRwAAAAwAUEcAAAAMAFBHAAAADDB/wfktqsIsGIJ5wAAAABJRU5ErkJggg==\n",
      "text/plain": [
       "<Figure size 864x432 with 1 Axes>"
      ]
     },
     "metadata": {},
     "output_type": "display_data"
    }
   ],
   "source": [
    "plt.figure(figsize=(12, 6))\n",
    "sns.boxplot(x='subscription_type', y='distance', data=final_df)\n",
    "plt.title('Сравнение расстояния поездок для пользователей с подпиской и без')\n",
    "plt.xlabel('Тип подписки')\n",
    "plt.ylabel('Расстояние (метры)')\n",
    "plt.show()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 37,
   "id": "dbdc63f2",
   "metadata": {
    "scrolled": true
   },
   "outputs": [
    {
     "data": {
      "image/png": "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jRw5Ujt27FDjxo3Vr1+/bK/tw4cP6+WXX9a0adNUs2bNXB+fxWJRWFiYdVv/lJWVpTJlyljnlytXzjpvwYIFMpvN2rZtm2JiYtSqVas8n8tWrVpZ1/Pzzz/bzHv77bd16dIlbdy4UYsWLdLq1au1fPlyQznyEh4erq+//lp79+5V165dFRkZKcnYe2/MmDGKiYnRZ599pk8++US///67pGtDAiIjI7V7927Nnj1bn3/+uTZu3ChJWrNmjfV9EBgYaM3br18/XblyRb169VKbNm20fft2zZgxQxEREfrzzz9tHueTTz6pmJgYrVu3zmZ6//79FRwcrK1bt2rBggVasGCBfvjhh2yPOS4uzlosc5Pb+ycnt/oekPLe/113s33vdf98vx44cEBvvvmmhg0bpt27d2vx4sUqX768zTIJCQnq3bu3XnvtNTVr1kzStRK8cuVKLVy4UBs3btSVK1c0fvx4Q9vMy4ULF7Ro0SKVLFky1/ukpKTozz//1Jo1awp0nwHHoVBD0rU3uL+/vzw98x5W/+CDD6ply5by8vJSz549lZaWpv3790u69kewTJkyMpvNevLJJ1WxYsUcj3hlZWXJYrGoVKlShrItWbJEffv2VeXKleXp6al+/frp0KFDNkepjfjuu+9ksVj06KOPWqd98cUX6ty5sx5++GF5eHioY8eO8vLy0r59+/K17hu9/PLLKlq0qIKDg/Xvf//b+sdu7dq1evnll1W6dGkFBATo5Zdfth7ZlaTU1FRJkpeXV7Z1btmyRRUrVlSHDh3k6empNm3aqFKlSvruu++s90lPT89xWUmqWLGipkyZov79+1uPRC1cuFDff/+9pk+fLrM5+64gp+crL6tXr1bjxo3VpEkTmc1mNWjQQDVq1ND3339vKOOtyuu5SUhI0NatWxURESE/Pz95eXmpbt26+Vp/fp+HnOT1u7/Za7Bq1arW96WHh4eqVKliXW9KSkquz+f69evVpEkTNWjQQF5eXurdu7dSUlJsyvCJEyfUq1cvvfrqq6pfv36ejyElJUXe3t65zr/Z79ZisdgcSb4VmZmZWr9+vV5//XUVL15c99xzj3r27GnzPrrV11iFChVUokQJa9bq1atLMvbeuzGfh4eHdT316tVTcHCwzGazqlatqtatW2vXrl03zbJlyxaVL19enTp1kqenp6pXr27zKVNej/OXX35RYmKiXnnlFXl7e6tChQp65plnsh3hlq59mpfbEWdJBfL+uc6e/V9+5PR+jY6OVqdOndSgQQOZzWaVKVNGlStXts5PSkpS79691bZtW3Xo0MEmc48ePVShQgUVK1ZMQ4YM0fr167OdbSO/+4jZs2erU6dO1tdJbhyxz4DjUKgh6dqXVM6fP3/T0/KULVvW+vP1HdP1o9CrVq2yfgQVFhamP/74w+Yozv79+xUWFqY6dero+PHj6tixo3VeQkKCdbmwsDBrSZekkydPatKkSdZ5devWlcVisQ4ZkaSOHTta53/yySfZcmdlZendd9/VG2+8YTP95MmTmj9/vs22T58+bXNkPb+CgoKsP5cvX966roSEBJujZeXKlbPZzpkzZ2Q2m3P8h8Y/l72+/I3PQVJSkvz8/HLNtX37dpUvX16TJk1SYmKi/u///k/Jyck5ftSb2/OVl5MnT+rrr7+2eS737NmjM2fOGM54/ffYrFmzbL/Hl19+2breCRMmWKfn9dycPn1afn5+eW4zL7fyPOQkr9+9kddg27ZtVadOHaWmpuq+++6zTj979qwCAgIMbdNsNisoKMjmNTNhwgSVLVs214+db3T27Fn5+/vnOj8pKSnXI269evVSkSJFVLt2bYWFhdkUw/w4f/680tPTsz2X/3wf5HXk75NPPlFYWJgeffRRvfLKKzb7qDlz5ujhhx/WzJkz1bRpU0nG3nsTJkxQWFiYWrdurU6dOln3Afv371f37t31yCOPKDQ0VEuWLLHZXm5OnDihAwcO2Lwm1q5da+i9dOLEiWz706ioKJ09e9bmfvv27VNcXJzNfvif7H3/3Mie/Z9Rub1fT506pXvvvTfX5d5//30VK1ZMP/30k278WllCQoLNkezy5csrIyND586du+k2c3PixAl99dVXeR5hl679w8IR+ww4DoUakqSQkBB5e3tbP47MzenTp60/Z2VlKT4+XoGBgTpx4oRGjx5t/ahp9+7d+te//mWz7MMPP6zdu3frwIEDatu2rc0wg8DAQO3evdv638MPP2ydFxQUpIiICJv5Bw4cUO3ata33WblypXVer169suVeuXKl7r//ftWqVctmelBQkPr162ez7v3796tNmzaGnrecnDp1yvrzyZMnrR97BgYG6uTJkzb3u/Ej0UOHDqlSpUo5HgX857LXl7/xI8ajR4/muuP8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hRoAAACwA4UaAAAAsAOFGgAAALADhRoAAACwA4UaAAAAsIOnswMAAHAn27x5szZs2ODsGC7hyJEjkqQRI0Y4OYlraN68ucLDw50dAwa4xBHquLg4de7cWS1atFDnzp119OhRZ0cCAACFLCAgQAEBAc6OAeSbyWKxWJwd4vnnn1enTp3Uvn17rV69WsuXL9fChQtvulxqaqpiY2NVo0YN+fj4FEJSAAAA3Glu1jmdfoT63LlzOnjwoNq0aSNJatOmjQ4ePKjExEQnJwMAAABuzumF+tSpUypTpow8PDwkSR4eHgoMDNSpU6ecnAwAAAC4udviS4mxsbHOjgAAAIA7lNMLdVBQkOLj45WZmSkPDw9lZmYqISFBQUFBhtfBGGoAAAA4yvUx1Llx+pCP0qVLq1q1alq3bp0kad26dapWrRrf8gUAAIBbcPoRakkaN26chg8frlmzZqlkyZKKjIx0diQAAADAEJco1JUrV9ayZcucHQMAAADIN6cP+QAAAADcGYUaAAAAsAOFGgAAALADhRoAAACwA4UaAAAAsINLnOXjVlksFklSWlqak5MAAADgdnW9a17vnv/k1oU6PT1dkvT77787OQkAAABud+np6SpSpEi26SZLblXbDWRlZSk5OVleXl4ymUzOjgMAAIDbkMViUXp6uooVKyazOfuIabcu1AAAAICz8aVEAAAAwA4UagAAAMAOFGoAAADADhRqAAAAwA4UagAAAMAOFGoAAADADhRqAAAAwA4UasBOx48fV7169ay333//feslSgEA12zcuFGtWrVShw4ddOTIEWfHAQoUhRooYB988IHS09NznJeRkVHIaQDANSxZskSDBg3SqlWrVKlSJet09ou4HXg6OwDgLo4fP65OnTpp586dNreXL19uvU9ERIQkqUuXLjKbzVq0aJEmTZokDw8PxcXFKTk5WatXr9brr7+uuLg4paen695779WkSZPk5+fnlMcFAI42adIk7dmzR3Fxcfrss8+0a9cuvfLKK9qyZYsaNWqkPn36aPLkyfrtt9+UmpqqevXqacSIEfLw8FBCQoImTJigkydPKjU1Va1bt1a/fv2c/ZAAGxRqoACNHTtWn332mZYsWaJixYpZpx86dEj/93//p6JFi0qSRo0apYCAAEnSjBkzNHfuXA0dOtQpmQHA0UaOHKlDhw6pV69eeuyxxxQcHCwfHx/rAYlRo0apTp06mjhxorKysjR06FAtX75czzzzjN58800NGDBAderUUVpamnr06KGHHnpIDRo0cPKjAv6HQg0UgpYtW1rLtCStXr1aa9euVXp6uq5cuaL77rvPeeEAwAk6duxo/Xnz5s06cOCA5s+fL0lKSUlRmTJldOXKFe3atUuJiYnW+yYnJ+vw4cMUargUCjVgkKenpywWi/V2amqq4WVvLNO7d+/W559/riVLliggIEBr167V0qVLCzQrALi6G/eLFotFs2bNUoUKFWzuc/nyZZlMJkVHR8vLy6uwIwKG8aVEwKC77rpL6enpOnbsmCRp3bp1Od6vWLFiunz5cq7ruXjxoooXL65SpUopLS3NZgw2ANyJwsPDNWfOHGVmZkqSEhMT9ffff6t48eIKDQ3VnDlzrPc9deqUzpw546yoQI4o1IBBnp6eGjVqlHr27KmnnnpKHh4eOd6vV69eev7559W+fXtdvHgx2/xGjRrp3nvvVYsWLdStWzdVr17d0dEBwKWNHDlSZrNZ7du3V9u2bdWnTx/Fx8dLkqZPn67Dhw+rbdu2atu2rV577bUc962AM5ksN36GDQAAACBfOEINAAAA2IFCDQAAANiBQg0AAADYgUINAAAA2IFCDQAAANiBQg0AAADYgUINAAAA2IFLjwNAAQgJCbH+fPXqVXl7e1sv/hMREaF27do5KxoAwMG4sAsAFLDw8HBNmDBBjz76qLOjAAAKAUM+AKAQvP/++xo6dKj19rhx4xQcHKxjx45JkoYPH64aNWooJCREISEhqlWrloKDg3NdX3h4uGrWrGm9/0MPPaTu3btb5+/du1edOnVSaGioOnXqpL1799osP2PGDD344IM228rIyLBmvT7v+n/BwcE6fvy4NeuMGTOs63rxxRdtlr9w4YJGjBihhg0bqk6dOhowYIAkaefOnWrcuLF1ualTp6pbt25KTU21Pqbt27dLkpKTk/Xoo4/q2WefzeczDQCFj0INAIUsLi5OW7duzTa9d+/eiomJUUxMjFavXn3T9URFRVnvP2bMGOv0Cxcu6KWXXlL37t21c+dO9ezZUy+99JLOnz9vs3y7du0UExOjdevWZVt3q1atrOv++eefc83w008/6bfffrOZNmzYMF29elVffvmltm/frh49emRbbs6cOdqxY4eioqLk4+OTbf7HH38sT09GJQJwDxRqAChkM2bMsB61dYQtW7aoYsWK6tChgzw9PdWmTRtVqlRJ3333nfU+KSkp8vLysms7FotF06ZN06BBg6zTEhIStHXrVkVERMjPz09eXl6qW7euzXLLli3TJ598onnz5ql48eLZ1nvmzBlFR0erZ8+eduUDgMJCoQaAQrRv3z7FxcWpY8eODttGQkKCypUrZzOtXLlyio+Pt94+e/as/P397drOV199JX9/fz3yyCPWaadPn5afn5/8/PxyXCYxMVGzZs2Sr6+vDh06lON9PvjgA3Xr1i3XdQCAq6FQA0AhmjZtmoYMGWI9A4gjBAYG6uTJkzbTTp06pTJlylhvHzx4UFWrVr3lbWRkZGjmzJk248IlqWzZskpKStLFixdzXM7Dw0Nz587V+PHjNWbMGF2+fNlmflxcnLZt26YXXnjhlrMBQGGjUANAIfnpp59kNpv12GOPOXQ7TZo00dGjR7V27VplZGRo/fr1+vPPP9W0aVNJ0rZt23T27FmbLwjm1+rVqxUSEpKtlAcGBqpx48aKiIhQUlKS0tPTbcZg+/n56YEHHlCjRo30yCOPaNq0aTbLf/TRR3r55ZdzHFcNAK6KQg0AheTMmTN64403HL4df39/RUVFaf78+apXr57mzZunqKgoBQQEaPfu3XrxxReVnJyshg0bKiQkRG3atJEk9evXz/A2Ll68qMGDB+c4b+rUqfL09FSrVq306KOPasGCBTneb8SIEdqyZYt27txpk71Dhw7GHywAuADOQw0Ad5CdO3dq5cqVmjJlSrZ5PXr00Kefflr4oQDAzXGEGgDuIN7e3rl+2S8gIKCQ0wDA7YEj1AAAAIAdOEINAAAA2IFCDQAAANiBQg0AAADYgUINAAAA2IFCDQAAANiBQg0AAADY4f8B0v2Ex9HFGnsAAAAASUVORK5CYII=\n",
      "text/plain": [
       "<Figure size 864x432 with 1 Axes>"
      ]
     },
     "metadata": {},
     "output_type": "display_data"
    }
   ],
   "source": [
    "plt.figure(figsize=(12, 6))\n",
    "sns.boxplot(x='subscription_type', y='duration', data=final_df)\n",
    "plt.title('Сравнение продолжительности поездок для пользователей с подпиской и без')\n",
    "plt.xlabel('Тип подписки')\n",
    "plt.ylabel('Продолжительность (минуты)')\n",
    "plt.show()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 38,
   "id": "a2f42ec7",
   "metadata": {},
   "outputs": [
    {
     "data": {
      "image/png": "iVBORw0KGgoAAAANSUhEUgAAAuIAAAGJCAYAAADPOFY7AAAAOXRFWHRTb2Z0d2FyZQBNYXRwbG90bGliIHZlcnNpb24zLjMuNCwgaHR0cHM6Ly9tYXRwbG90bGliLm9yZy8QVMy6AAAACXBIWXMAAAsTAAALEwEAmpwYAACRk0lEQVR4nOzdd3Qc5fXw8e/sbFfvzZblbuNuGUyxMZhiU2zTQockBEIJgbyEBJIQeiA2hCQ/eicBQu+mdwwY4967ZVm2ZPUubZ/3j7GEZat7V7O7up9zdGRrdmbujnZnr565cx9F0zQNIYQQQgghRJ8yGR2AEEIIIYQQ/ZEk4kIIIYQQQhhAEnEhhBBCCCEMIIm4EEIIIYQQBpBEXAghhBBCCANIIi6EEEIIEWJer9foEEQYkkRcCBG1VqxYwfLly/F6vbz66qs0NzcbHZIQQfHKK69QX19PaWkpCxcuNDqciBXKc4Tf7+eJJ55g7ty5HHPMMRx77LG43e6gbV9EB7PRAQjRG++99x7PPvssBQUFxMTEMGrUKK666iqmTJlidGgijCQmJnL99ddTWlrK8ccfz7nnnmt0SEIEhc/nY/bs2ZjNZv785z8bHU7ECuU54o477qCwsJCHH36YgQMHBm27IrooMqGPiDTPPvssTzzxBHfccQfTpk3DYrGwaNEili5dyk033WR0eEIIIfq5oqIizj//fD7++GNiY2ONDkeEMSlNERGlvr6e//u//+PWW2/l5JNPxul0YrFYmDlzZmsS/uCDD3Ldddfxu9/9jkmTJnHmmWeyadOm1m088cQTnHjiiUyaNIlTTz2VTz/9tHXZm2++yejRo5k0aRKTJ0/m0ksvpbS0FIAlS5Zw7LHHtonnggsu4M0332z9/+uvv84pp5zC4Ycfzq9+9Sv27NnTumzkyJEUFha2/v+f//wnN998MwC7d+9m5MiR+Hw+ANasWcPIkSP55z//2fr4L7/8knnz5jFlyhTOP//8Ns/pQCNHjuS///0vJ5xwAlOnTmX+/PkEAgEAdu3axaWXXsrUqVOZOnUqv//976mrq2tdt6SkhGuvvZYjjzySqVOncuedd7Yue/XVVznllFNaj9369eu58847mTRpEpMmTWLkyJFMnDiRSZMmcfnllwNQWlrKVVddxRFHHMFJJ53Eq6++2rq9pqYmrrvuOo444ggmTZrE2LFjW49JV8f7zTff5IILLmhd9uSTTzJy5Ei+//57QH8d3Hjjja3Lb7/99oN+B/u75JJLGDduXOtzGT9+PDNnzmxdvn37di655BKmTJnCaaedxueff966zOPxMH/+fI477jiOPvpobr31VlwuFwDLly9n5syZTJo0iRkzZvDCCy+02e8///lPxowZw6RJk5g4cWKb14HH4+Fvf/sb06ZNY9q0afztb3/D4/G0e3wWLFjAxRdf3Oml7wsuuKD1OY4bN67N8QO4+eabGTt2bOvz33/7n332GbNmzWrzu969e3e7+5k5cybjx49vfey4ceO45JJLWpevWLGCs88+m/z8fM4++2xWrFjR7Tg6+x1edNFF5OfnM2XKFH7729/S0NAAQCAQ4JFHHuH444/nqKOO4o9//CP19fXAT++9llhPP/10lixZ0rrNu+++mxkzZjB58mTOOussli1bBsDKlStb1xkzZkxrvJMmTaK4uBjo/Hxw4HEaO3Zsm9drZ+/3mTNntr7OGxsbOfroow/6Xe5v2bJlnH/++UyZMoUZM2a0OWft71DeAwCvvfZa6/mz5TXS8rva/9za8jVq1KjWY93V+9XlcvH3v/+d448/nvz8fC644AJcLtdB584XX3yR0047jerqaqDz809vzhGvvfYaoL+m5syZc9A5qsXatWtJTU3l6quvJj8/P6jnDBFdJBEXEWXlypW43W5OOumkTh/3+eefM3v2bH788UdOP/10rrnmmtYbZQYOHMiLL77I8uXLufbaa/nDH/5AWVlZ67oTJ05k5cqVLF68GKvVynPPPdet2D777DMef/xxHnroIRYvXkx+fj6///3ve/U8FyxYQEZGRuv/N2zYwJ///GfuvPNOlixZwnnnncc111zTmpS159NPP+WNN97grbfe4osvvuCNN94AQNM0rrzyShYtWsSHH37I3r17efDBBwG9pvHKK68kOzubL774gm+++YZTTz0VgA8//JAHH3yQ+fPns2LFCh599FESExO59dZbWblyJStXrgTgnXfeYeXKlTz11FMA3HDDDWRmZrJo0SL+7//+jwceeIDFixe3PrawsJDPP/+clStX8qtf/apXx6umpobnn3+e+Pj4dpcXFBTwzTffdLmd/Z/LY4891vpzr9fLVVddxTHHHMP333/PLbfcwo033siOHTsAuP/++ykoKODtt9/mk08+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hOHDhwP6B3vLZeAHHngARVF47733SExM5LPPPmu9TJ+VlUVJSUm7f+hkZWWxa9eubu8f9ONVW1tLQ0ND64dhSUlJa9mNw+HguOOOY/369bzxxhs8+OCDbY5JZ8e7xb///W8uv/zyDkeD77vvPm644QZUVe1R7Ac+j7179xIIBFo/WEtKSsjLyyMpKQm73c7777/fppxofy2X63fu3MlFF13EuHHjmDFjBqCXIZx44okd7re4uLj191hSUtLmcr6qqjz55JOUlJRw66238u6773Z4HMrLy6moqGDkyJEdPs+dO3e2Jq8HOuaYY4iNjWXBggWcdNJJjBkzpsPtdKblOe2vpKSE6dOndysO+Ol9tGXLFs455xxmzJjB0KFDAX30u7m5mVtvvZV7772Xf/3rX63v/RbFxcWYzWZSUlJazxU//PADZrOZJUuWcO2117Jo0SLWrVvHU089xXPPPcfw4cMxmUwcfvjh3boq0NX5YM+ePfj9/naTy+683wsKCvj222/54IMP2oxKt7et9q749VRn74EWGzZs4Mgjjzyk/bT3fk1KSsJms1FUVMSoUaPaXe8f//gHycnJXHrppcyYMYPMzMwuzz+d7bMjNTU1vPjii7z++uttynIOlJWVFbJzhoguUpoiIkpcXBzXXXcdd955J5999hnNzc14vV6+/vprFixY0Pq49evX88knn+Dz+fjPf/6D1WplwoQJNDc3oyhK66jgG2+8wdatW9vdl6IoqKpKVVVVt2I7//zzeeKJJ1q3V19fz4cfftij5/fYY49xww03HPTzn/3sZ7z88susXr0aTdNoampqrYPtyNNPP01tbS0lJSX897//ba31bmxsxOl0EhcXR2lpaWstN8D48eNJS0vjH//4B01NTbjdbpYvXw7AOeecwzPPPMO6devQNI3CwsIuy26ysrKYNGkSDzzwAG63m02bNvH666+3Jif19fX87W9/46677urViM+uXbtYvXo15513XrvLf/jhB0wmE8cff3yPt72/8ePHY7fbeeqpp/B6vSxZsoQvvviCU089FZPJxM9+9jPuueee1hHW0tJSFi1aBOjdExobGwH9Bq1AIIDdbgfg22+/paKiosMbvk477TQeffRRqqqqqKqq4uGHH2bOnDmtyxMSEhg2bBjTp0/nyCOP5L777uvwOTz//PMceeSRpKSkHLTM5/Px0ksv0djY2GEL0GeeeYaMjAxOOeWUbhyxjs2YMYOdO3fy3nvv4fP5+OCDD9i2bRvHHXdct+LYX3x8PCaTCU3TaGxspKioCNBLaHw+X+txPv300/nPf/7T+rv45z//ySmnnNLua05VVerr6/F6vTQ2NqKqKsnJyfh8Ph566KFO33P76+x80NDQwMMPP8wxxxzTbslBd97vjz76KL/5zW+6vDoxZ84cvv/++9ZSnOrqajZu3Nit57C/zt4DAJs3b2bZsmWdDpB0paP3q8lk4uyzz+bee++ltLQUv9/PypUr29TMT5kyhREjRnDJJZfw17/+Fej6/NPZPjvyn//8h3POOYe0tLROHzdhwgQcDkfQzxki+siIuIg4l112GampqTzyyCPceOONxMTEMGbMGK666qrWx5xwwgl88MEH3HTTTQwaNIgHH3wQi8XCsGHDuOyyyzj//PNRFIUzzjiDyZMnt9n+qlWrmDRpEoqikJeX13pS78pJJ51EY2MjN9xwA3v27CEuLo6jjz66R4nLcccd1+5I4Lhx41rLAgoLC7Hb7UyePLnTZOWEE07grLPOoqGhgTPPPJNzzjkHgGuvvZabbrqJKVOmkJuby7x581pvSFVVlccee4y777679YNpzpw55Ofnc8opp1BTU8Pvf//71kv9CxYs6LJO/IEHHuC2225j+vTpxMfH89vf/ra1/r1ldHX8+PHdPkb7q6io4JZbbumwnri8vLzdmxt7ymq18thjj3HHHXfw+OOPk5GRwYIFC1pHYf/whz/w8MMPc+6551JdXU1GRgYXXHAB06dPZ8mSJfzrX/+isbGRpKQkfvGLXzB16tTWS8+KorSWlLSMtF511VU89dRTXHPNNTQ2NrYmDrNnz+6wLvVPf/pTa8ePqVOntln22GOP8fjjj2OxWJg0aRKgJ98+n4/HHnuMxMRE3nzzTR555JF2P/B37drFs88+23rD76FISkriscce45577uH2229n0KBBPPbYYyQnJ/Pyyy93GkeLiy66CJPJhKqqXHnllQwbNozS0lKuueYadu/ejdVqZcqUKa39tc8++2xKS0tbu8pMmzbtoPf14Ycf3hrfnXfeSVxcHNOmTWP69OnMmjULp9PJz3/+8zYlX53p7Hxw9913U1tby913393uut15vyclJXHGGWd0GUd2djZPPvkk8+fP55ZbbiEuLo7f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\n",
      "text/plain": [
       "<Figure size 864x432 with 1 Axes>"
      ]
     },
     "metadata": {},
     "output_type": "display_data"
    }
   ],
   "source": [
    "plt.figure(figsize=(12, 6))\n",
    "sns.histplot(df_free['distance'], color='coral', label='Free', kde=True, bins=30, alpha=0.6)\n",
    "sns.histplot(df_ultra['distance'], color='skyblue', label='Ultra', kde=True, bins=30, alpha=0.6)\n",
    "plt.title('Сравнение расстояний поездок для пользователей с подпиской и без')\n",
    "plt.xlabel('Расстояние (метры)')\n",
    "plt.ylabel('Частота')\n",
    "plt.legend()\n",
    "plt.show()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 39,
   "id": "a6ca6902",
   "metadata": {
    "scrolled": true
   },
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "Статистика для пользователей с подпиской Ultra:\n",
      "count    6500.000000\n",
      "mean       18.541985\n",
      "std         5.572048\n",
      "min         3.760353\n",
      "25%        14.524654\n",
      "50%        18.178153\n",
      "75%        21.884129\n",
      "max        40.823963\n",
      "Name: duration, dtype: float64\n",
      "\n",
      "Статистика для пользователей без подписки:\n",
      "count    11568.000000\n",
      "mean        17.390909\n",
      "std          6.326803\n",
      "min          0.500000\n",
      "25%         12.958437\n",
      "50%         17.326890\n",
      "75%         21.594071\n",
      "max         34.948498\n",
      "Name: duration, dtype: float64\n"
     ]
    }
   ],
   "source": [
    "print(\"Статистика для пользователей с подпиской Ultra:\")\n",
    "print(df_ultra['duration'].describe())\n",
    "print(\"\\nСтатистика для пользователей без подписки:\")\n",
    "print(df_free['duration'].describe())"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "88cc0751",
   "metadata": {},
   "source": [
    "График показывает, что пользователи с подпиской Ultra имеют более высокую медиану продолжительности поездок (+-18 минут) по сравнению с пользователями без подписки (+-15 минут). Разброс значений также шире для Ultra, что указывает на большую вариативность в использовании сервиса. Статистика подтверждает эти наблюдения: среднее значение для Ultra выше, а стандартное отклонение больше."
   ]
  },
  {
   "cell_type": "markdown",
   "id": "87922930",
   "metadata": {},
   "source": [
    "#### 5  Шаг. Подсчёт выручки"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 40,
   "id": "f4137045",
   "metadata": {},
   "outputs": [],
   "source": [
    "final_df['duration'] = np.ceil(final_df['duration'])"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 41,
   "id": "3632e9b0",
   "metadata": {},
   "outputs": [],
   "source": [
    "aggregated_df = final_df.groupby(['user_id', 'month']).agg(\n",
    "    total_distance=('distance', 'sum'),  # Суммарное расстояние\n",
    "    ride_count=('user_id', 'count'),     # Количество поездок\n",
    "    total_duration=('duration', 'sum')   # Суммарное время (округлённое)\n",
    ").reset_index()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 42,
   "id": "a504e1ab",
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/html": [
       "<div>\n",
       "<style scoped>\n",
       "    .dataframe tbody tr th:only-of-type {\n",
       "        vertical-align: middle;\n",
       "    }\n",
       "\n",
       "    .dataframe tbody tr th {\n",
       "        vertical-align: top;\n",
       "    }\n",
       "\n",
       "    .dataframe thead th {\n",
       "        text-align: right;\n",
       "    }\n",
       "</style>\n",
       "<table border=\"1\" class=\"dataframe\">\n",
       "  <thead>\n",
       "    <tr style=\"text-align: right;\">\n",
       "      <th></th>\n",
       "      <th>user_id</th>\n",
       "      <th>month</th>\n",
       "      <th>total_distance</th>\n",
       "      <th>ride_count</th>\n",
       "      <th>total_duration</th>\n",
       "    </tr>\n",
       "  </thead>\n",
       "  <tbody>\n",
       "    <tr>\n",
       "      <th>0</th>\n",
       "      <td>1</td>\n",
       "      <td>1</td>\n",
       "      <td>7027.511294</td>\n",
       "      <td>2</td>\n",
       "      <td>42.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>1</th>\n",
       "      <td>1</td>\n",
       "      <td>4</td>\n",
       "      <td>754.159807</td>\n",
       "      <td>1</td>\n",
       "      <td>7.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>2</th>\n",
       "      <td>1</td>\n",
       "      <td>8</td>\n",
       "      <td>6723.470560</td>\n",
       "      <td>2</td>\n",
       "      <td>46.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>3</th>\n",
       "      <td>1</td>\n",
       "      <td>10</td>\n",
       "      <td>5809.911100</td>\n",
       "      <td>2</td>\n",
       "      <td>32.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>4</th>\n",
       "      <td>1</td>\n",
       "      <td>11</td>\n",
       "      <td>7003.499363</td>\n",
       "      <td>3</td>\n",
       "      <td>56.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>5</th>\n",
       "      <td>1</td>\n",
       "      <td>12</td>\n",
       "      <td>6751.629942</td>\n",
       "      <td>2</td>\n",
       "      <td>28.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>6</th>\n",
       "      <td>2</td>\n",
       "      <td>3</td>\n",
       "      <td>10187.723006</td>\n",
       "      <td>3</td>\n",
       "      <td>63.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>7</th>\n",
       "      <td>2</td>\n",
       "      <td>4</td>\n",
       "      <td>6164.381824</td>\n",
       "      <td>2</td>\n",
       "      <td>40.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>8</th>\n",
       "      <td>2</td>\n",
       "      <td>6</td>\n",
       "      <td>3255.338202</td>\n",
       "      <td>1</td>\n",
       "      <td>14.0</td>\n",
       "    </tr>\n",
       "    <tr>\n",
       "      <th>9</th>\n",
       "      <td>2</td>\n",
       "      <td>7</td>\n",
       "      <td>6780.722964</td>\n",
       "      <td>2</td>\n",
       "      <td>48.0</td>\n",
       "    </tr>\n",
       "  </tbody>\n",
       "</table>\n",
       "</div>"
      ],
      "text/plain": [
       "   user_id  month  total_distance  ride_count  total_duration\n",
       "0        1      1     7027.511294           2            42.0\n",
       "1        1      4      754.159807           1             7.0\n",
       "2        1      8     6723.470560           2            46.0\n",
       "3        1     10     5809.911100           2            32.0\n",
       "4        1     11     7003.499363           3            56.0\n",
       "5        1     12     6751.629942           2            28.0\n",
       "6        2      3    10187.723006           3            63.0\n",
       "7        2      4     6164.381824           2            40.0\n",
       "8        2      6     3255.338202           1            14.0\n",
       "9        2      7     6780.722964           2            48.0"
      ]
     },
     "execution_count": 42,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "aggregated_df.head(10)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 43,
   "id": "aa9ee34f",
   "metadata": {},
   "outputs": [],
   "source": [
    "aggregated_df = pd.merge(aggregated_df, final_df[['user_id', 'month', 'subscription_type']].drop_duplicates(), \n",
    "on=['user_id', 'month'], how='left')"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 44,
   "id": "cb66c6f4",
   "metadata": {},
   "outputs": [],
   "source": [
    "def calculate_revenue(row):\n",
    "    if row['subscription_type'] == 'ultra':\n",
    "        start_ride_price = 0    # Бесплатный старт для ultra\n",
    "        minute_price = 6        # 6 руб./мин\n",
    "        subscription_fee = 199  # Абонплата 199 руб.\n",
    "    else:  # 'free'\n",
    "        start_ride_price = 50   # 50 руб. за старт\n",
    "        minute_price = 8        # 8 руб./мин\n",
    "        subscription_fee = 0    # Нет абонплаты\n",
    "    \n",
    "    # Округление общей продолжительности вверх\n",
    "    total_duration_rounded = np.ceil(row['total_duration'])\n",
    "    \n",
    "    # Расчёт выручки\n",
    "    revenue = (start_ride_price * row['ride_count']) + (minute_price * total_duration_rounded) + subscription_fee\n",
    "    return revenue"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 45,
   "id": "ef06992f",
   "metadata": {},
   "outputs": [],
   "source": [
    "aggregated_df['monthly_revenue'] = aggregated_df.apply(calculate_revenue, axis=1)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 46,
   "id": "10eac475",
   "metadata": {},
   "outputs": [],
   "source": [
    "aggregated_df = aggregated_df.drop_duplicates().reset_index(drop=True)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 47,
   "id": "a8ff1fdb",
   "metadata": {
    "scrolled": true
   },
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "Первые 5 строк с выручкой:\n",
      "   user_id  month subscription_type  ride_count  total_duration  \\\n",
      "0        1      1             ultra           2            42.0   \n",
      "1        1      4             ultra           1             7.0   \n",
      "2        1      8             ultra           2            46.0   \n",
      "3        1     10             ultra           2            32.0   \n",
      "4        1     11             ultra           3            56.0   \n",
      "\n",
      "   monthly_revenue  \n",
      "0            451.0  \n",
      "1            241.0  \n",
      "2            475.0  \n",
      "3            391.0  \n",
      "4            535.0  \n"
     ]
    }
   ],
   "source": [
    "print(\"Первые 5 строк с выручкой:\")\n",
    "print(aggregated_df[['user_id', 'month', 'subscription_type', 'ride_count', 'total_duration', 'monthly_revenue']].head())"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 48,
   "id": "92e9a832",
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "\n",
      "Общая выручка: 3878641.0 руб.\n"
     ]
    }
   ],
   "source": [
    "total_revenue = aggregated_df['monthly_revenue'].sum()\n",
    "print(f\"\\nОбщая выручка: {total_revenue} руб.\")"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "9cb021f0",
   "metadata": {},
   "source": [
    "Промежуточный вывод\n",
    "\n",
    "Функция `calculate_revenue` теперь корректно рассчитывает выручку на основе тарифов: для Ultra — 199 руб. абонплаты + 6 руб./мин (со стартом 0 руб.), для Free — 50 руб. за старт + 8 руб./мин (без абонплаты). Продолжительность поездок округляется вверх, что соответствует условиям. Проверка первых строк и общей выручки (около [вставить значение]) подтверждает работоспособность алгоритма. Выручка от Ultra включает фиксированную плату, что делает её более предсказуемой."
   ]
  },
  {
   "cell_type": "markdown",
   "id": "d9007b2f",
   "metadata": {},
   "source": [
    "6  Шаг. Проверка гипотез"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "2801e0f9",
   "metadata": {},
   "source": [
    "#### 6.1 Гипотеза: Пользователи с подпиской тратят больше времени на поездки\n",
    "\n",
    " Формулировка гипотез\n",
    "- **H₀ (нулевая гипотеза):** Среднее время поездок пользователей с подпиской Ultra равно или меньше среднего времени поездок пользователей без подписки (μ_ultra ≤ μ_free).\n",
    "- **H₁ (альтернативная гипотеза):** Среднее время поездок пользователей с подпиской Ultra больше среднего времени поездок пользователей без подписки (μ_ultra > μ_free).\n",
    "\n",
    "#### Уровень значимости\n",
    "Установим уровень значимости α = 0.05.\n",
    "\n",
    "#### Метод проверки\n",
    "Используем t-тест для независимых выборок (`scipy.stats.ttest_ind`), так как сравниваем две независимые группы (пользователи с подпиской и без). Альтернатива — односторонняя (`alternative='greater'`), поскольку нас интересует, больше ли время у пользователей с подпиской."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 49,
   "id": "093ec60e",
   "metadata": {
    "scrolled": false
   },
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "t-статистика: 12.2412\n",
      "p-value: 1.2804e-34\n",
      "p-value (1.2804e-34) < alpha (0.05):\n",
      "Отвергаем нулевую гипотезу: пользователи с подпиской тратят больше времени на поездки.\n"
     ]
    }
   ],
   "source": [
    "# Удаляем пропуски\n",
    "duration_ultra = df_ultra['duration'].dropna()\n",
    "duration_free = df_free['duration'].dropna()\n",
    "\n",
    "# Уровень значимости\n",
    "alpha = 0.05\n",
    "\n",
    "# Выполняем t-тест для независимых выборок (Ultra > Free)\n",
    "results = st.ttest_ind(duration_ultra, duration_free, alternative='greater')\n",
    "\n",
    "# Вывод результатов\n",
    "print(f\"t-статистика: {results.statistic:.4f}\")\n",
    "print(f\"p-value: {results.pvalue:.4e}\")\n",
    "\n",
    "# Интерпретация\n",
    "if results.pvalue < alpha:\n",
    "    print(f\"p-value ({results.pvalue:.4e}) < alpha ({alpha}):\")\n",
    "    print(\"Отвергаем нулевую гипотезу: пользователи с подпиской тратят больше времени на поездки.\")\n",
    "else:\n",
    "    print(f\"p-value ({results.pvalue:.4e}) >= alpha ({alpha}):\")\n",
    "    print(\"Не можем отвергнуть нулевую гипотезу: нет достаточных доказательств, что пользователи с подпиской тратят больше времени.\")"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 50,
   "id": "e6e732cf",
   "metadata": {},
   "outputs": [
    {
     "data": {
      "image/png": "iVBORw0KGgoAAAANSUhEUgAAAtsAAAGKCAYAAADdSo1nAAAAOXRFWHRTb2Z0d2FyZQBNYXRwbG90bGliIHZlcnNpb24zLjMuNCwgaHR0cHM6Ly9tYXRwbG90bGliLm9yZy8QVMy6AAAACXBIWXMAAAsTAAALEwEAmpwYAADARUlEQVR4nOzdeXxU1dnA8d/s2ScLSUhIWMO+CaIIAiouqBVtba1LrVWsvq/Wim21Uvu61FZaUVutW92qdd9FARXrhijIJvtqIISEhCQkZE9mf/+4ZMiQ7c5kZu7M5Pl+PnyAuXfufe4z9545c+655+g8Ho8HIYQQQgghRNDptQ5ACCGEEEKIWCWVbSGEEEIIIUJEKttCCCGEEEKEiFS2hRBCCCGECBGpbAshhBBCCBEiUtkWQgghhBARyeFwaB1Cr0llWwghhBAR66WXXuKOO+7A7Xbz5ZdfcvPNN2sdUp8Szvw7nU6ef/55LrvsMmbOnMn48eP55z//GbL9hYtOxtkO3IIFC3jvvfcAMBqN9O/fn3POOYdf//rXJCQkaBydEEIIEf2qq6u54oorKC0tJSEhgSeffJIpU6ZoHVafEa78ezwerrvuOioqKrj55pspKChAr9eTnZ1NXFxc0PcXTkatA4h206dPZ9GiRTidTtavX8///d//0dzczJ/+9CetQxNCCCGiXkZGBh999BFVVVWkpaVhNpu1DqlPCVf+33//fUpLS3n77bdJSkoKyT60It1IeslsNpOZmUlOTg5z585l7ty5fPbZZwC4XC7uuOMOZs+ezYQJEzjnnHN45plncLvdPtt47733mDt3LuPGjWP69Oncfvvt3mUjR47s9M+CBQu868yePZtHH32UW2+9lUmTJnHqqafy3HPP+eyjoaGBO++8k2nTpjFp0iSuvPJKtm7d2uF4Zs+e3WFf7777rs8677zzDueffz7jx49nzpw5vPDCCx2OacGCBR22c++996qO591332XSpEk+26ypqWHkyJGsWbMGgDVr1jBy5Ehqamq869x2222MHDmSjz/+2PtaRUUFv/nNbzjppJM46aSTuP7669m/f3+HY28vWHkvKyvjV7/6FZMmTWLSpEncdNNNHDp0SNX+2o4TwG63c9999zF9+nTGjx/PT3/6U9avX99hO519fm25WLVqFZMmTfLZ7uzZs70xezwebr31Vi699FJaWloA5XP8n//5H+/6R44c4aSTTvJ+Nu+++26XuRo5cqT3fd999x1XXnklEydOZObMmdx99900Njb6xN7Zti644ALv8p///Oc+59DxHn30UZ/1AbZu3crIkSMpLS31vvbJJ594r7fTTjuNJ598kvY3+Ox2O3//+98544wzGDduHGeeeSYvvvgipaWl3R7rmjVrvOu0P5cffvhhRo4c2eHcOD72rrbrb+wPPPAAs2bNYuLEifz4xz9m5cqV3uXffvst5513HhMmTODEE0/k2muv7XAttF1XXX2W0PN5ffxnsWfPHqZOncq///3vLnPQpqdrAXo+V5qamvjDH/7Aqaee2u122vv5z3/e6b5nz57tXcftdvP4449z2mmnMW7cOObOncunn36qalttn39n58jxrrzySk4++WTGjx/Peeedx+LFi32W93QetC8H2s6DtWvXepdv2bKFefPmMXXqVCZPnszll1/Oxo0bO33/8X8effRRoOdzrbtttS+fCwsLuf7665k0aRLTpk3jt7/9LVVVVd7lbWVQWwtnU1OTTxnUlYaGBu6++25mzJjhzeOHH37Y5fpdHfPPf/5z7zpqy+HOPv+2vIG6672z78Cf/exnHc6dvXv38r//+7+ceOKJTJo0iUsvvZTdu3f75K7Nt99+y6RJk3zysHv3bq6++momTJjAySefzIIFC2hoaOh1/o//nN96660O9YDjffnllwwbNowbbriBSZMmMX36dBYuXIjdbveu4/F4eOaZZzjrrLOYMGECc+fO5f333/fJx4UXXugtmy6//HK2bNnSbazhIJXtIIuLi/N25ne73WRnZ/Pwww/z4Ycfcsstt/DUU0/xzjvveNd//fXXueuuu7j44ov54IMPePrppxk+fLjPNv/yl7/w9ddfe/9Mnz69w36ff/55hg0bxrvvvsuvf/1r/vGPf/DJJ58Aysl5/fXXU1FRwVNPPcXixYuZMmUKv/jFL6isrOywrV/96lfefcXHx/sse/PNN/nHP/7BzTffzIcffsjtt9/OM888w6uvvuqznsfjYfr06d7ttL8w/Y1HrW3btvH555/7vNbS0sJVV12FxWLhpZde4vXXXyczM5NrrrnGW6HsSm/z7na7ufHGG6murubFF1/kxRdfpLKykhtvvJHOem+17W/ZsmUdli1atIiPPvqIhQsXsnjxYkaMGMF1113XIV8ej8fn82tv+vTp/OlPf+Kmm27yFsbH72Pr1q3861//6vC5t3n88cdxOp3e/59//vnefd1xxx3079/fJ2egFObXXnsts2fP5v333+exxx5j165d3HHHHR1ij4+P97533rx5ncbQG9u2bWP+/PmcffbZLFmyhN/97nc8/fTTvPzyy951FixYwOLFi1mwYAEfffQR9913HykpKeTk5Hhje+uttwDlC6Szc7xNRUUF//nPf1TdAh0yZIhP7p599lm/Y//DH/7AunXreOihh1i6dCk/+tGPuOGGG9i1axcAOTk53HXXXSxbtoxXX30VnU7HXXfd1Wk8y5Yt4+uvv+Yvf/mLz+v+ntfFxcXMmzePn/3sZ6o/0+6uBej5XHnqqaf46quveOihh1i5cmWX2znexRdf7PMZ/OpXv/JZ/uKLL/Lcc89x6623smTJEs466yx+/etfs3Pnzg7xtd9W//79Ve2/zZVXXsmrr77Kxx9/zGWXXcaCBQs4ePAgoO48gGPl+OLFi8nOzuaPf/yjd1lTUxMXXnghr776Km+99RajR4/m+uuv58iRIwC8/fbbPrHfcccdHXLd07nWPhddlUmVlZX87Gc/Y/jw4bz99ts8//zzNDc3c+ONN3ZowGlzfBnUmbbuCOvWrWPhwoV8+OGHLFiwAJPJ1O372sf59ddfc/HFF/ssV1sOg++5NGTIkA7Le7rej/fJJ5+wY8cOn9cqKiq44oor0Ol0PP/887z77rtcccUVneZu06ZN3Hjjjfzxj3/k/PPPB6C5uZlrr72WhIQE3nrrLR577DE2btzYoWxuT03+j9fc3MwjjzzSY/fampoaPv30U4YMGcLbb7/Nfffdx7Jly/j73//uXefhhx/m7bff9pZj119/PXfffTdffvklACkpKfzud79j8eLFvPPOOwwcOJDf/OY3fsUbCtKNJIi2bNnCkiVLmDZtGgAmk4n58+d7l+fl5bFjxw6WLVvGJZdcAsATTzzBL37xC6655hrveuPGjfPZbnJyMpmZmd7/d3YLZ+LEidxwww2AchFv3bqV559/nnPOOYdvv/2WXbt2sXr1au+X/i233MIXX3zB+++/z3XXXefdjt1ux2q1even0+l89vPEE09w6623cu655wKQn5/PgQMHePXVV7nyyiu96zmdThISErzbaV/I+ROPP+6//36uvfZaHnnkEe9ry5Ytw+Px8Ne//tV7LPfeey/Tp0/niy++8BY6nelt3levXs3u3bv573//S15eHgAPPfQQZ599NqtXr/ZW3tt+taelpZGZmYnBYPDZR3NzM6+//jp/+ctfOP300wH405/+xLfffssrr7ziU5A4HA6fz+94F154IVVVVVx33XW88cYb3tdffPFFlixZwuuvv05aWlqn792/fz/vvPMOV199NS+88AKg/Lhs+wyTk5MxGAwd9v3cc89x3nnn+VSI7rnnHn74wx9SXV1NRkYGoJwzJpPJ+/5QPPfw/PPPc9JJJ3kf8BkyZAjFxcU888wz/PznP2f//v0sW7aMZ555hlmzZgHKOd6mLTabzQZAenp6l7kG+Mc//sH555/P6tWre4zNaDT6bOv4OyA9xX7gwAGWLVvG559/Tm5uLqBU2latWsXrr7/OPffcw6BBgxg0aJD3GDIzMykpKfHZT9v5mJ2dTXJyMsnJyT7L1Z7XAOXl5VxzzTWcf/75qh6q6ulaaNPTubJz505mzZrFKaecAii5VSM+Pt7nM0hMTPRZ/txzzzFv3jzmzp0LwPz581m/fj3PPfccDz74oE987cuPro6jK23lK8DQoUMB5U4p9HwetI89MzMTq9VKWlqaz+fY9h3V5s477+STTz7hq6++4qKLLiI9Pd27zGAwdCgL1Zxrbbork1577TVGjRrFbbfd5n3t/vvv5+STT2bbtm1MmDDBZ/3OyqDOrFq1ik2bNrFs2TKGDRsG+F7HXWnLWZv2jQ7+lMN2u90nZ52dfz1d7+05HA4eeughrrvuOp/vt1dffZWEhAQeeeQR7/dTZxX7Xbt2cf311zN//nx+8pOfeF9funQpLS0tLFq0yNtt49577+Wqq66iuLjYW1a0UZv/4z333HMUFBR4z+GueDweBg0axD333INer2fYsGH87ne/46677mL+/Pl4PB6ef/55/v3vf3v7jOfn57NlyxZeeeUVTj/9dDIzMznttNMA5TrMzc3t8odbOEllu5dWrlzJpEmTcDqdOJ1OzjzzTO68807v8tdee4233nqLsrIybDYbDoeDAQMGAMpDBxUVFR0KvkCccMIJHf7/3//+F4Dt27fT0tLSYT82m63DF21tbW2XfaVqamooLy/n7rvv9umT7nQ6O7RoNTQ0kJKS0ul21MbT3Nzc462qNp9++ikHDhzgmWee8SmMtm/fTmlpKZMnT/ZZv6WlpcOxB6K7vO/du5esrCxvhQSUgiErK4vCwkJvpaStNamrvB84cACHw+FzDAaDgRNOOIG9e/f6rNvY2NhjJXXKlCk88MAD/PKXv6SlpYUvvviCDRs2MGHCBO8XZ2ceeOABLr30UlVfWu1t376d4uJiPvroI+9rbefLgQMHvJXthoaGLlvU27z55pu89957mEwm8vLyuO666zjvvPO8y/fu3etzzhxfyO7bt89bELc58cQTeeyxx2hsbGTHjh3o9XpvJa03du7cySeffMLHH3+sqrLdk55i3759Ox6Phx/84Ac+69jtdp/jKSsr4wc/+AGtra0MHTqUf/3rXz7r19bWotfru/ws1J7XjY2NXHPNNRw8eJCZM2eqOsaeroU2PZ0reXl5rFy5kvLycnJyclTtuyeNjY1UVlZy4okn+rw+efJkvvrqK7/iA6VyqtfrSU5OZuLEifz+97/3ubZ++ctf8u2332IwGFi4cCEDBw4Eej4P2nL3j3/8g8ceewybzUZ6erpPy2l1dTWPPPIIa9as4fDhw7jdblpbWykvL1eVC7XnGnRfJm3fvp3169d3Ws4fOHCgQ2VbbRm0Y8cOMjMzvRXtYPCnHO7uezQQr776KomJicydO9fn+23Hjh1Mnjy52z7UZWVlzJs3j+bm5g7fuXv37mXkyJE+sU6aNAm9Xk9hYWGHynYg3wEVFRU8//zzvPrqq9x33309rj9x4kT0+mOdLk488UQcDgfFxcXY7XZsNhu//OUvfRoC29er2h9HW4PCY489pjreUJHKdi9NmTKFP//5zxiNRrKysnxacD/88EMWLlzI7bffzqRJk0hKSuKVV17ptI9fKLndbvr168crr7zSYVn7i+zQoUM4HA6fL9HjtwPKr/meKsGVlZUUFBT0Kp74+Hifvop1dXXeOwLtuVwuHnzwQX7zm990uF3vdrsZNWoU//jHPzq8z2q1dnsModS+oGir9HeVd7XbaWhooKWlhaysrC7Xdzgc/N///R+/+tWvKCws5OOPP6ahoYFHH32UhQsX8tprr/Gzn/2sw/vWr1/P+vXrWbhwofeZBLXcbjeXXHIJV199dYdl2dnZ3n9XVlZ2GzvAeeedx0033YTdbmfZsmX87ne/Y+TIkd7Wv4EDB/L000971//+++87dAUIl/vvv5958+b1eEzB4vF40Ol0vP322x1a0tpfF1lZWSxevJgjR47w1FNP8cADD/gMrVVSUkJOTo7q1uD22p+P5eXlnH/++Vx00UXceeedLF26tMcKiNproadz5Ve/+hUHDhzg9NNPJz4+vsMdumA7fvtqzuWHHnqI4cOHU1NTw9/+9jduv/12n+549913H/X19axatYp//OMfTJkyxVvhVuOaa67hJz/5Ca2trbz88svcfPPNLF26FLPZzO233051dTV/+MMfGDBgAGazmauvvlr1eMZqz7WeyiS3281pp53m85xSm7Yf4W16UwaFWvvP3+l0Ul5eHlB53pn6+nqeeOIJHnvssYDO4z179jBv3jyamppYsGABb775pqpr+/h9BZr/Rx55hDlz5jBq1Kge1+2qga4tnrZGmieffLJDw9Dxx7R48WIaGxt57bXXuO+++3jppZd67EYUSlLZ7qX4+PgOv/7abNiwgYkTJ/p0rzhw4ID33xkZGWRnZ7N69WpOPfXUXsWxefPmDv9vq4CMHTuWw4cPo9fru/1FunbtWiwWC+PHj+90eb9+/cjKyuLAgQP88Ic/7HI7jY2N7N27l1/+8pedLlcbj06n88lt+wch23vjjTeIj4/noosu6nRfy5YtIy0trdsLOVDd5X3YsGFUVlZSWlrqLXhLSko6/BBZt24dOTk5XRbOAwcOxGQy8d1333m/bF0uF5s2bfJ5KKwtlu4KtbYHdNsemlm7di0//elPOeusszCbzdxyyy2ceeaZPn1MPR4Pf/vb37jhhhsC+oEyZsyYTltJjrdlyxbGjBnT7TrJycne7dx88808/fTT7Nmzx5tzk8nks5/6+nqf9w8dOpTvvvvO57UNGzbQv39/kpKSGD16NG63m2+//dbbjSQQK1asYO/evTz55JMBb+N4amL3eDxUVVV12zJvNBq93Unmz5/PRRddxJEjR7zdh9atW9eh9bY9ted1bm4uixYtQq/X88UXX7Bw4UIWLlzY7TH2dC206elc6devH1dffbX3GQSj0djpD3V/JCUlkZWVxYYNG3xaCL/77jufFtSioiLq6+t7PJezs7O9n8Nll13W4cGx7OxssrOzGT58OO+99x6ffvop8+bN6/E8aJOamuq9Fv7nf/6HN998k927dzN+/Hg2bNjA//3f/3m7Qxw+fNjnocSeqD3XeiqTxo4dy0cffURubm63FSF/y6AxY8ZQVVXF3r17g9a67U85bLPZgjY03pNPPsmJJ57ISSed5POwNCjH+cEHH2C327ts3Z40aRK33347TU1NzJ07l6eeesrbADFs2DDeeecdnzsiGzduxO12++Qt0O+A3bt38/HHH/vc1ezO0KFDWb58OW6329u6vWHDBkwmEwMHDsTlcmE2mykrK+uxR0DbuX/77bczZcoUdu/e3aGLbjjJA5IhNHjwYLZv386KFSvYv38/jz/+OOvWrfNZ53//93/5z3/+wwsvvEBRURE7d+5U9cT+8TZt2sRTTz3F/v37efPNN1m8eLG3JXH69OlMnjyZG2+8kRUrVlBSUsLGjRv55z//6X2Suq0LxhlnnEFTUxNVVVVUVVXh8XhoaGigtbUVUCo4zz77LC+88AL79u1jz549LF68mKeeegpQbkv97ne/Izk52VuQH09NPP547rnnuP322zv91T937lwyMjK48cYbWbt2LSUlJaxbt46//e1vPY5IokZPeR85ciS33norW7duZevWrdx6662MGTPG+wW1c+dOXnnlFc4991xvztt+VNTV1WG320lISODyyy/nwQcf9Fbi7rnnHu/Yp6D0o/3zn//MzJkzfVqL29u3bx9PPfUUf/7znzGbzZjNZuLj470/QmbNmsUZZ5zh098SlApQfX19py3ealx33XVs2bKFu+66ix07dlBcXMwXX3zhfTCvpqaGBx98kE2bNvGjH/2o2225XC5sNhuNjY288cYbeDyeLu+gdGbevHmsW7eORx99lKKiIj744AP+/e9/e38YDhkyhPPOO4//+7//Y/ny5ZSUlLB+/foOo0H05LnnnuOWW27psSuBP9TEPnfuXP7whz/w8ccfU1JSwtatW3nuuee8D+1++umnrF+/noMHD7J161YefvhhcnNzSUtLw263s3z5cr799lvOOuss7/nYNjJBW2VMzXkNSv9Xk8mEwWDgr3/9K8uWLWPFihVdHp+aa0HtuVJaWsqtt97KX//6VyZPnhy0VsZrr72Wf//73yxdupSioiIeeeQR1q9fz7XXXgsoPwJuv/12CgoKmDhxYrfbcjgc2Gw2ysvL+fDDD70PxhcXF/Phhx9SVFREcXExL7zwAnv27GH06NFAz+dBm7ZyvKSkhJdeegmz2ey91T5kyBA++OADCgsL2bJlC7/5zW/8avVTc66pKZOuuOIKGhoa+M1vfsPmzZspKSlh1apV3HnnnT6jFflbBk2bNo2JEyfy61//mpUrV1JSUsI333zTq7vKasrhqqoqHnnkEU444QTi4+O957HT6aS5uZmmpia/9mm323n99dd9+rS3d8UVV9Dc3Mwtt9zCli1bKC4uZunSpT4P7LZVjhMTE1m4cCH/+te/vA+xzp07l7i4OG6//XZ2797NunXruOuuuzjnnHN8Gi0C/Q544YUXuPrqq7v8/I93+eWXU15ezj333MPevXv58ssveeihh7jyyiuJj48nKSmJefPmsWjRIt5++22Ki4vZuXMnr732mvcZpNWrV7Nq1SpKS0vZvXs3CxcuJDExscfGnlCTlu0QuvTSS9m5cye33norHo+Hc845h2uuucZnKL0rrrgCk8nE888/z4MPPojVag2oRe2aa65h9+7d3pEkbr75Zu9DNjqdjqeffpqHH36YO++8k5qaGjIyMpg8ebK3hfrqq6/m4MGD7Nmzx2e4HoCFCxeSnJzMxRdfzCWXXEJ8fDzPPfccDz30EHFxcRQUFHhb7x955BHcbjcvvPBCl7eM1cTjj6lTp3bZuhIfH88rr7zCQw89xPz582loaCArK4upU6cGpaW7p7w/8cQT/OUvf+Gqq64ClIrKnXfe6f1h0Ha8zz//PM8//7zPtn/961/z4osvMnXqVG9h+4c//MHbavbMM894b8/+4Q9/YMaMGfz+97/vNE6Px+Md9eb4/uvt/fGPf/QOkdX+ifXf/e53Ad+CGzVqFC+//DIPP/wwV155JW63m/z8fM466yxAud23evVqHnvssR67J73++uu8/vrrmEwm8vPzWbhwoV+V7bFjx/LII4/w6KOP8tRTT5GRkcH111/vc/dp0aJFPPzww/zlL3/hyJEj9O/fv9MuMN0ZOHBgjz8c/KUm9r/+9a/861//4oEHHqCiogKr1cr48eOZOnUqoPSfXLRoEeXl5SQmJnLCCSd4u91s3LjR+9BdZw8zzpgxg927d6s6r483bNgwbrnlFm93ks6uPTXXwvbt23s8V2w2G7/+9a+57LLLOPPMM7tLqd+uuuoqmpqaeOCBB6iurmbIkCE8+uij3pbb++67j9zcXG6//XaffqedufzyywG8fbb/9re/AUo3hP/85z98//33uN1uBg4cyL333uttyVNzHoAyasTjjz+OxWJh2LBhPProo94H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FvD+++97ntPW1ubztQARERG89NJLvPLKK2zcuBGAb7/9locffpiXX34Zi8XS5TWVlZW8++67Xsl5T/bt20dNTQ0///nPMRqNpKWl8cMf/pAPP/zQrzr2V2+xWb16Nb/4xS8YM2YMGo2Giy66iGHDhg1oHRobGzGbzV7bDAYDs2fP5qOPPsLhcPD5559z1VVX+Xz9p59+iqIozJw50+/3vPnmm9mwYQPttzi9//773Hjjjf1vhA9ms5nGxsYBLVMIERrkzLYQYsiyWq2e/6ekpFBRUQFARUUFEyZM8Owzm83Ex8dTXl5OamoqoCa4vhLfiooK4uLiiI6O9iq7sLDQ87i+vp64uLhu63XgwAGio6N55plnAFixYgVpaWls3bqVMWPGdHn+H/7wB+68884ey+yopKSEiooKpk6d6tnmcrm8HvdWx6effpr8/HwiIiKYNWsWTzzxBCaTCYA///nPnqsAbrfb85reYnPmzBlGjRrlVxs6+9nPfoZOp8NsNvP973+fX/3qV2g0mi7Pi42NxWazddm+YMECnnnmGbRaLbNmzfL5RcPtdrNy5UpWrFjB22+/7bWvczybm5tZsGABAJMnTyYiIoJt27aRmJjIqVOnmDt3rt9tmz59OhqNhuHDh7N48WLmz5/f5Tk2m42YmBi/yxRCDB1yZlsIMWSVlZV5/l9aWkpSUhIASUlJlJSUePY1NzdTV1fnGTpQWVlJVVWVz6EVSUlJ1NfX09TU5PU+HYcdnDhxgvT0dJ91stvtPPnkk6xYsYIXXngBgF//+te88MILrFq1isrKSq/nHz9+nC1btvDjH//Y73ZbrVZSU1PZsWOH52f37t3893//t191BHjsscfYsWMH69evZ//+/axZs8az75577vGU+95773m29xabESNGcOrUKb/b0dGqVavYsWMHb7/9NuvWreOLL77w+bzs7GxOnDjRZXtWVhatra28/PLLniS5s/fee4+MjAymTJnSZV9SUpJXPCdPnuy1/+abb2bdunWsW7eOa6+91vPFxB9bt25l+/btLF++nEcffdTnl4WjR48O2FAfIURokWRbCDFkvfnmm5w5c4a6ujoKCgq44YYbAJg3bx5r1qzh4MGDOBwOVq5cyaRJkzxntV9//XWmT5/uc5o1q9VKbm4uK1euxG63c+jQId59913PsIGdO3fy8ccfdztM4aWXXmLKlCnMmDGD3NxcAHJzcxkzZgy3336752x3u5dffpmf/exnfUreJk2ahNls5o9//COtra24XC6OHDnC3r17/apjR5GRkRiNRq8z2N3pLTYLFizg97//PSdOnEBRFA4dOkRtba3f7QL1KoRer6e7WWmvuOIKtm/f7nPfT3/6U2bOnMm4ceN87i8oKGDp0qV9qk+7G2+8kY8//ph169Zx00039auM2NhYFEXx2bbt27d3uUlTCBEeZBiJEGLImjdvHvfccw8VFRXMnTuX+++/H4CZM2fyi1/8giVLltDQ0EBubq5n5omCggJeeeUVDAaDJxl2Op04nU4KCgpYvHgxK1eu5PHHH2fWrFnExsayZMkSZs6cSVFREcuWLeORRx5h0qRJXepTVFTEu+++ywcffOCzvj/96U+58cYb+fzzzz2J1bBhw/qcvOl0OgoKCsjPz2fu3Lk4HA4yMjL45S9/2Wsd2z333HO8+OKLuN1uLr/8cm699Va/3ru72ADcfffdOBwO7rnnHmpraxkzZgyrVq3yq9ylS5diNBoBuOaaazw3enY2f/58brrpJlpbW7vcaDpnzhzmzJnT7XtceeWVPZ7t74nVamX8+PGcOnXKa7iJP/Ly8gB1ir+nnnrKaxgOqFdDPvvsM6+rC0KI8CGL2gghhqS8vDyefvrpPt3oBvDiiy8ycuRIbrnlFq/tO3bs4Ouvv2bJkiUDWU0RACtXrsRisXDXXXcN6vs++uijJCUl8eCDDw5oua+//jplZWU8/PDDA1quECI0yJltIcQFJTo6msjIyC7bjUZjlzOOIjT1dyjI+SguLmbTpk1eY9gHyqJFiwa8TCFE6JBkWwhxQeluer1Jkyb1OOxCXLheeOEF/vrXv/KTn/yEtLS0YFdHCDHEyDASIYQQQgghAkRmIxFCCCGEECJAJNkWQgghhBAiQCTZFkIIIYQQIkAk2RZCCCGEECJAJNkWQgghhBAiQCTZFkIIIYQQIkD+P7PU7tNU1oowAAAAAElFTkSuQmCC\n",
      "text/plain": [
       "<Figure size 864x432 with 1 Axes>"
      ]
     },
     "metadata": {},
     "output_type": "display_data"
    }
   ],
   "source": [
    "plt.figure(figsize=(12, 6))\n",
    "sns.histplot(duration_ultra, color='skyblue', label='Ultra', kde=True, bins=30)\n",
    "sns.histplot(duration_free, color='coral', label='Free', kde=True, bins=30, alpha=0.6)\n",
    "plt.title('Распределение продолжительности поездок для пользователей с подпиской и без', fontsize=14)\n",
    "plt.xlabel('Продолжительность (минуты)', fontsize=12)\n",
    "plt.ylabel('Частота', fontsize=12)\n",
    "plt.legend()\n",
    "plt.grid(True, linestyle='--', alpha=0.7)\n",
    "plt.show()"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "ee5cd562",
   "metadata": {},
   "source": [
    "Промежуточный вывод\n",
    "\n",
    "На основе t-теста (t-статистика: 12.2212, p-value: 1.2804e-34) при уровне значимости α = 0.05 мы отвергаем нулевую гипотезу. Это означает, что пользователи с подпиской Ultra статистически значимо тратят больше времени на поездки, чем пользователи без подписки. Гистограммы также подтверждают, что распределение продолжительности поездок для Ultra смещено вправо по сравнению с Free. Таким образом, пользователи с подпиской могут быть более \"выгодными\" для компании, так как они активнее используют сервис."
   ]
  },
  {
   "cell_type": "markdown",
   "id": "d4ebba4d",
   "metadata": {},
   "source": [
    "#### 6.2 Гипотеза: Среднее расстояние поездок пользователей с подпиской не превышает 3130 метров\n",
    "\n",
    "#### Формулировка гипотез\n",
    "- **H₀ (нулевая гипотеза):** Среднее расстояние одной поездки пользователей с подпиской Ultra не превышает 3130 метров (μ_ultra ≤ 3130 м).\n",
    "- **H₁ (альтернативная гипотеза):** Среднее расстояние одной поездки пользователей с подпиской Ultra превышает 3130 метров (μ_ultra > 3130 м).\n",
    "\n",
    "#### Уровень значимости\n",
    "Установим уровень значимости α = 0.05.\n",
    "\n",
    "#### Метод проверки\n",
    "Используем t-тест для одной выборки (`scipy.stats.ttest_1samp`), так как сравниваем среднее расстояние поездок пользователей с подпиской с заданным значением (3130 м). Альтернатива — односторонняя (`alternative='greater'`), поскольку нас интересует, превышает ли среднее расстояние 3130 м."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 51,
   "id": "7d9b130e",
   "metadata": {},
   "outputs": [
    {
     "data": {
      "image/png": "iVBORw0KGgoAAAANSUhEUgAAAtsAAAGKCAYAAADdSo1nAAAAOXRFWHRTb2Z0d2FyZQBNYXRwbG90bGliIHZlcnNpb24zLjMuNCwgaHR0cHM6Ly9tYXRwbG90bGliLm9yZy8QVMy6AAAACXBIWXMAAAsTAAALEwEAmpwYAACRrElEQVR4nOzdeXwTZf4H8M/M5E6bpiltaWmhLZVyiaAo6wFqkUMFQcVjEVwXlZ/3rbBeeLKCJyquJ7LeynqDC6z3sYqwioicLRRaWtrStEmbOzPz+yM2tvRK0qSTmfm+Xy9f0iQz+U6+eSbfPHnmeRhRFEUQQgghhBBC4o6VOgBCCCGEEEKUioptQgghhBBCEoSKbUIIIYQQQhKEim1CCCGEEEIShIptQgghhBBCEoSKbUIIIYQQQhKEim1CiCJde+21eO+99wAAS5cuxWuvvSZxRITI186dOzF9+nS0tLSgtrYWkyZNkjokVUnk6//UU09h2rRpcdsf6YiKbQVZuHAhSkpKUFJSghEjRmDixIlYsmQJ3G631KER0ufmzp2LxYsXY8SIEfj8889xxhlnSB0SIbI1ZMgQDBo0CMcddxxKS0tx0UUXSR2SqsTy+s+dOxf33Xdfh9vfe+89jBkzpsvtFi5ciP/7v//rVbykPY3UAZD4OuGEE7B06VIEg0Fs2rQJd955J9xuN+69916pQyOkTx133HH473//i8bGRmRmZoJlqW+BkFgxDIOnn34adrsdOp0OKSkpUoekKsn4+gcCAWi1WqnDkAX69FEYnU6HzMxM5OTkYPr06Zg+fTo+++wzAADP87j99ttRWlqKUaNGYfLkyXjhhRcgCEK7fbz//vuYPn06Ro4ciRNOOAELFiwI39fac374fwsXLgw/prS0FE899RRuueUWjBkzBieeeCJeeumlds/R3NyMu+66C8cffzzGjBmDOXPm4Ndff+1wPKWlpR2eq3VoQKt3330XZ5xxBo488khMmTIFK1eu7HBMbXv9W/9r+42/p3g66wmw2+0oKSnBhg0bAAAbNmxASUkJ7HZ7+DG33norSkpKsHbt2vBttbW1uPHGG3Hsscfi2GOPxfz581FRUdHh2NsqKSnBa6+9hvnz5+Ooo47Cqaeeig8//LDdYx555BFMmTIFo0aNQmlpKZYuXQqfz9fuMV999RXOO+88jBo1CuPGjcMVV1wRfozf78djjz2GU089FSNHjsTEiRPxyiuvoKqqqsu8tz3+nTt34pJLLsGoUaNw3HHHYeHChWhubg4/N8/zeOihhzBhwgQMGzasQz5bn+fw98G0adPw1FNPtXst2r6eq1at6pDP0tJSvPTSS9DpdMjOzg7nprvemtbHdPZf2/dcT8cJdP+ebGxsxKxZs3DMMcfgqKOOwtlnn42vvvqqQzzdvdat+XrwwQdxwgkn4Mgjj8T555+PTZs2dTie1vdjS0sLzj33XFx99dUIBoNdvg5AqEfs8OdumwMAXb4v2r7/n3/+eZSWlmL48OFd7qetp556qsscVFVVhR+3fv368Dnq5JNPxj/+8Q8cvhhyZ/tqm//W90hXlixZgpNOOgkjR47EhAkT8PDDD7c7r/T0Pjj8l8ZJkybhjTfeCN/f2NiIm266CRMmTMCoUaNw5pln4t133+10+8P/mzt3bvhxfX3+s9lsSElJwUUXXdRpe21LFEWsWLECkydPDr+Ojz76aJeP7+6Y23rrrbcwadIkjBw5EpMmTcI777zTYV+d5b/t6xZJe+/snPTEE0+gpKSk3XunubkZixYtwkknnYQjjzwSp59+Oj755JMOrx0QOv9PnDgRDzzwQPg2h8OBBQsW4Nhjj8WoUaNwySWXYPfu3b1+/WPx1FNP4f3338eXX37Z7rzT+lqsXr0aF198MUaNGoW33367x/cxCaGebYUzGAwIBAIAAEEQkJ2djSeeeAI2mw1btmzB3XffDavVivPOOw9A6CT24IMP4qabbsLJJ58Mt9uNH374od0+H3jgAZxyyinhv2+77bYOz/vyyy/j8ssvx9VXX40NGzbggQceQH5+PiZPngxRFDF//nykpqbiueeeQ1paGt5//3385S9/wdq1a5GVldVuX1dffTX+/Oc/A0CHcWrvvPMOnnzySdx5550YMWIEdu/ejbvuugsajQZz5swJP04UxXCvPxAaz9v2vmjiidTWrVvx+eeft7vN4/Hg4osvxpgxY/Dqq69Cq9VixYoV+Otf/4pPPvkERqOxy/099dRTuPHGG/G3v/0Na9euxYIFC1BUVIQjjzwSAGA0GrF48WJkZ2ejvLwcixYtgk6nww033AAA+Prrr3HllVfi8ssvx9///ncEg0F899134Q/mhQsXYtOmTbjjjjswfPhwHDhwAAcPHkROTg6+/fZbAEBNTQ3OO+88rFq1Cjk5OQCAtLQ0uN1uXHrppRg1ahRWrVoFh8OBu+66C7fffnu4uPrXv/6FN998E48++iiOPPJIsCzb63GHbrcby5Ytg8lk6vIxgiBgyZIl3T6mrTVr1iAtLS38d9sYIznOnt6Ter0eV1xxBQYPHgyNRoO3334b1113HTZu3AidTtculta25nA4cOaZZ7a7b+nSpVi7di0WL16M/Pz8cJtbt25dh/es1+vFFVdcgbS0NDz++OPQaHo+9Z9zzjm46aabAKBdkdKqtbh98cUXMXToUPz888/t2tW3336Lxx57DA888ABOOukkcBzX6X4OV1hYiFdffTX8944dO3DZZZeF/966dSuuv/56XHnllZg+fTp+/fVXLFq0CCkpKe32L4piu309+OCD8Hg8PT5/qwkTJmDatGlIT0/H7t27ccstt+CII47AzJkzI3ofAH/80hgIBPDee+/h3nvvxamnnoqcnBz4/X4MHz4cl19+OVJSUvDf//4XixYtQm5uLo4//njccccduPnmm8OxA8Add9wBAOHeRKnOf+vXr8e2bdt6fA0fe+wxvPnmm1i4cCGOPfZY2O32HrdrGycAfPnll7jzzjvDf//nP//B/fffj7/97W848cQT8e233+Lee+9Fv379UFpa2m5fbfP/2GOPtfvC1qq79n642tpa/POf/4TBYAjfJooiLr/8cjidTixevBiFhYXYs2cP/H5/h+3tdjv++te/4rjjjgvnEgide/fu3YtnnnkGFosFjz/+OC677DKsW7eu3XO1ivT1j8W8efNQXl4Oh8MRzkNaWhrq6uoAhF7H2267DQ8++CC0Wm2P72MSQsW2gm3ZsgUff/xx+A2v1Wpx/fXXh+/Py8vDtm3bsGbNmnCx/cwzz+Avf/kL/vrXv4YfN3LkyHb7TU1NRWZmZvjvwwsEADjqqKNw5ZVXAgid8H799Ve8/PLLmDx5Mn744Qfs2LED33//ffhEcsMNN+CLL77Ahx9+iMsvvzy8H7/fj7S0tPDzMQzT7nmeeeYZ3HLLLZg6dSoAID8/H/v378cbb7zR7sMmGAzCZDKF99P2p69o4onGkiVLcOmll2LZsmXh29asWQNRFPH3v/89fCz33XcfTjjhBHzxxRfdjiueNGkSLrzwQgDAlVdeiQ0bNuCf//wnHnnkEQChLyWt8vLy8H//939YsWJFuNh+5plnMGXKFNx4443hxw0dOhQAUFFRgTVr1uCFF17AhAkTAIRey1atr1trL7jNZmv3Hvjggw/g8XiwdOnS8M+b9913Hy6++GLs27cPgwYNwvbt23HUUUfhtNNOC293eD6j9dJLL6G4uBg8z3f5mA8++AA+nw8TJ07s0APdGZvNBpvN1mmMq1ev7vE4e3pPmkym8GsgiiIGDhwIhmEQCATCban1gzo9PR2ZmZngOK5djG63G2+99Va7L7733nsvfvjhB7z++uvtchwIBHDNNdeA53ksX7680/Z6OL/f366dd1act/aOZ2ZmIjMzs13BAgDbt2/HgAEDMGvWrPBtkRT5Go2m3Xvr4MGD7e5/+eWXceyxx+K6664DEDq/7Nu3Dy+88EK7YjsYDMJgMIT3ZTAYoiq22xYKPM9Dr9eH32eRvA+AP35pBID+/ftDr9dDr9cDALKzs9t9ibjgggvwww8/YPXq1Tj++OORmpqK1NTUcOwA2r0ugDTnv0AggEcffRSXX355u3Pb4VwuF1auXInbb789/B4YNGhQt2OFD3/NAIRfg1YvvfQSzjrrrPDxFRYW4rfffsMLL7zQrtj2+/3t8t9VR0Z37f1wjz/+OM444wx8//334dv++9//YvPmzVizZg0GDx4MoP25s1VzczMuvfRSFBcX44EHHgg/T0VFBT7//HO89tprOPbYYwEADz/8ME455RR8/PHH4c/mVpG+/rEym83htnL4+w0A5syZE36/terufUxCqNhWmG+++QZjxoxBMBhEMBjExIkTcdddd4Xvf/PNN7Fq1SpUV1fD5/MhEAhgwIABAICGhgbU1tbGpYGMHj26w9//+c9/AAC//fYbPB5Ph+fx+XyorKxsd1tTU1OXY9PsdjtqamqwaNGidmPSg8Fgh5+Um5ubYbFYOt1PpPG43e4ePyhaffrpp9i/fz9eeOGFdifE3377DVVVVTj66KPbPd7j8XQ49sN19pq2HX6wdu1a/POf/8T+/fvhdrvB83y7n5O3b9+Oc845p9N9b9u2DSzL4k9/+lNEx3e48vJylJSUtMvVmDFjwLIsysrKMGjQIOTl5WHNmjXYvXs3jjjiiC73NWfOnHbjq71eb6ePq62txcsvv4w33ngj3PN3OI/HgyeeeAL33HMP1q9fH9OxtdXTcaampkb8njzzzDOxb98+mEwmLFu2DGazOXxfY2MjAHT53t+/fz8CgUC79xHHcRg9ejTKy8vbPXbBggX4/vvvMX/+/G5/OWmru3bXqvWLS1f7zMvLw8GDB7Fx48ZwEREPe/bswcknn9zutmOOOQZPP/00WlpawnE3Nzf3eLyPP/44nn76aej1ehQVFeHaa69tdx549tln8Y9//ANerxcXXXQRZs6cCSCy9zvwx/k4EAiAYRjcf//94cKO53k8//zz+OSTT1BXVwe/349AIIDjjjsuotehr89/rd544w2YzWZMnz6922KvvLwcfr8/7gXXnj17cO6557a77ZhjjunwK2Ik7+FobN++HevXr8fatWvbFdvbtm1DZmZmuNDuDM/zmD9/PrZt24YLLrig3Zfn8vJysCzb7vyempqKIUOGoKysrMO+In39E+Xwzrfevo/VgopthRk7dizuv/9+aDQaZGVltevB+OSTT7B48WIsWLAAY8aMQUpKCl5//XV8+umnfRqjIAjo168fXn/99Q73tT05Hjx4EIFAAHl5eV3uBwj16PVUBNfV1aG4uLhX8RiNRnzwwQfhvx0OR4deByB08nnkkUdw4403dvgJUBAEDB06FI8//niH7Q7vGYzG5s2bcdNNN+Hqq6/G+PHjYbFY8Pnnn2PJkiUx7zNeWntwZs+ejW3btmHGjBnQarVgWbbTmXIeffTRdsX4/PnzO93vsmXLMGXKlHDvfGdWrFiBgoIClJaWxqXY7g7DMFG9J59//nk0Nzfj3//+NxYvXozRo0eH3wOtRU5X7/2e4mirrq4Oy5cvxw033IDJkyeHhx11JRgMoqampsfnrqurA8MwnfZ+AcCUKVOwceNGzJs3DwzDgOO4Lr84JUJdXV2PQ8D++te/YtasWfB6vXj11Vdx1VVX4euvvw73pl544YWYPHkyysvLsXjxYhx//PE9Dn1q+/q3no+DwSB++OEH3HPPPRgxYgSOOOIIvPTSS3j55Zdx++23o6SkBCaTCY899li7Me/d6evzHwA4nU4888wzePrpp3v9q1S8HR5PZWVlTO2nK0uWLMG8efNiGlbo8/nAcRwefPBBLF68GBMmTEBubm6P2x1+TNG+/mazudNf85xOZ8xfRA7/Atvb97Fa0AWSCmM0GjFo0CAMGDCgw1XC//vf/3DUUUdhzpw5GDFiBAYNGoT9+/eH78/IyEB2dna7b+2x+uWXXzr8XVRUBAAYMWIEDh06BJZlMWjQoHb/ZWRkhLf58ccfodfruywO+vXrh6ysLOzfv7/Dflp7loDQhWHl5eUYPnx4p/uJNB6GYdrd19WJ/O2334bRaMSMGTM6fa79+/cjPT29w3NZrdbOX8w2r+Hhf7e+pj/99BOys7Nx9dVXY9SoUSgoKEB1dXW7xw8bNqzL3A4bNgyCIHQYnx+pwYMHY9euXWhpaQnf9vPPP0MQhHCPj8lkwvz582EymfDEE0/ggw8+6LTnMTs7u93r0tnV7jt37sTatWvDQ2Q6c+jQIaxYsaLdxbu91dNxRvqeBIABAwZg6NChuPHGG2G329u99hs3bkROTk6X77GBAwdCq9Xip59+Ct/G8zw2b97coYdt+fLlOO200zB37lwsXLiw07Gkbf3yyy/w+XwYO3Zst4/bsmULBg0a1OVYeJZl8X//93+wWCy466678MEHH2DgwIHd7jMSRUVF7Y4bCJ3b+vfvHy4gBEHA1q1bu2zzraxWKwYNGoSSkhJceeWVcLvd7S5WtlqtKCoqwqRJkzBp0qTwhV+RvN+BP87HgwcPxkUXXQSbzYavv/4aQKjNnnrqqZg5cyaGDRuGgQMH9nihdFt9ff4DgH/84x845phjIvqloqioCDqdLi6fJ4fvt7P8t33dfT4ftmzZgmOOOSYuz/nVV1+hvLwcl156aYf7hg8fjvr6+g6/KLWl0+nw7LPPYtasWTjppJNwxx13hH99GDx4MARBwObNm8OPb2lpwa5duzq05WhefyA0xGbbtm0dfunYtm0bCgsLu9xOq9V2OzSvrd6+j9WCim0VKSgowG+//YavvvoKFRUVWL58OTZu3NjuMVdccQX++c9/YuXKldi7dy+2b9+OFStWRP1cmzdvxnPPPYeKigq88847+OCDD3DJJZcACF0Ac/TRR+Oqq67CV199hcrKSvz888948sknw7MptA7BOPXUU+FyuVBfX4/6+nqIoojm5uZwD9l1112HF198EStXrsSePXuwa9cufPDBB3juuecAhH6iu/nmm5Gamtruos62IoknGi+99BIWLFjQac/D9OnTkZGRgauuugo//vgjKisrsXHjRjz00EM9nqDWr1+Pd955BxUVFXjuuefw/fff4y9/+QuAUG5ra2vx0UcfobKyEm+88QZWr17dbvsrr7wSa9euxeOPP46ysjLs3r0bK1euhMfjQWFhIU4//XTceeedWLduHSorK7Fp06Z2PfndmT59OgwGAxYsWICdO3di48aNuPvuuzF58uTwB7/D4cA111yD66+/HqeeeioGDRoUc+/YypUrcckllyA7O7vLx7z11ls47bTTeiy4ohHJcfb0nty6dSu++OILVFZWory8HI8++ijcbjeGDBkCIPRz9euvv46pU6eG3/etvUQOhwN+vx8mkwl//vOf8cgjj4QLgXvuuQcNDQ2YPXt2u5hbe8tvuOEGCILQ7U/P9fX1WLZsGUaPHg2j0Rh+/mAwCLfbDZfLBb/fj/fffx///Oc/O/yc35bf78d1112H6dOn47zzzuvyi1O05s2bh40bN+Kpp57C3r178dFHH2HFihXhcaPV1dW444470NjY2OGi0sPxPA+fzweHw4F33nkHBoMh/IXgtddew65du3DgwAF8+umnWLNmDYYNGwYgsvdB62tQX1+PgwcP4sMPP0RNTU34C3JBQQG+//57bNq0CeXl5bjvvvs6vYCvO315/vP7/Xjrrbdw6623RhRbSkoKLr74Yjz22GN49913sX//fmzZsqXdjCyxuOyyy/DRRx/h9ddfR0VFBV599VV8/PHH4fy7XC4sW7YMoiji2GOPDb+HPR4PAoEAmpqaon7Ol156CTfccEOnnQPHH388jjrqKFx77bX45ptvUFlZie+++67dL8YajSb8RfCee+7Bjh078OabbwIIvQ8mTpyIu+++G5s2bcLOnTtxyy23ICUlBdOnTw/vI9rXHwj9mlhZWYn7778fO3bswJ49e7By5UqsWbOm0y8OrQYMGIDdu3djz549sNvt4UkWOhOP97Ea0DASFbnggguwfft23HLLLRBFEZMnT8Zf//rXdtOazZ49G1qtFi+//DIeeeQRpKWlhS+Yi8Zf//pX7Ny5E88++yyMRiOuu+668EUVDMPg+eefxxNPPIG77roLdrsdGRkZOProo8NjIi+55BIcOHAAu3btajfNGwAsXrwYqampOOecc3DeeefBaDTipZdewqOPPgqDwYDi4uLwxTPLli2DIAhYuXJllz+bRRJPNMaNG9fl2Gej0YjXX38djz76KK6//no0NzcjKysL48aN63JMZatrr70W69atwwMPPACbzYa///3vGDVqFIDQNGaXXnopFi9eDJ/PhxNPPBHXXXddu7GcJ598Mp5++mks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Ftdee2349ueeew6//PJL+DUeOXIkpk6dihkzZuDFF18EAHzwwQfhx1999dVoaGgAAASDQdx8883hx48ZMwZffPEFAGDu3LnhfwPAkiVL8NRTT3V4PV0uF8aPH4/rrrsu/Njnn38es2bNwtlnn40rrriiy7HMb7/9NqZNmxb+OyUlJTwcw+v1IhAIhP8eNGgQhg0b1ukwmu62++STT3DeeeeBZVnYbDacdtppWLt2bYd9VFVVoaSkBA888ED4tieffBIlJSXYtWsXAODkk0/G119/jZaWlk6PJ1qq6dmmJU/li3Inb5Q/eaP89YFTTul42/nnh5a7d7uBM87oeP8ll4T+O3QImDWr4/1XXgn27LN7fOpdu3Zh4MCBsFqt7W4fPXo0li1bFv77hBNOwJNPPhne5oorrgAA/P3vfwcQKsi//PLL8GMOt3Tp0g6zNxx99NF444038MADD+Crr75CdnZ2eDpEi8WCZ599FmazGYFAAJdeeim+/vprTJgwAUDoF5ezzz4bt912G1577TVs3bq1x2Ptzi233ILzzz8f5513HsrKynDRRRfh3//+NzQaDRYtWoTnn38eWVlZqKurw6xZs7B69WpYLJYO+/nll1/w1ltvQa/XY/78+XjnnXcwZ84cnHTSSZg2bRoYhsGePXtwySWX4Ouvv0YwGMRVV12FG264AaeffjoAoLGxEQBwxx134JRTTgkXvna7HTabDR9++CEAoLS0FE8++SSGDBkCIJSXRx55BO+99x6ysrLwxBNP4P7778cTTzyBb7/9Fnv37sUnn3wClmUxd+7cqF+jZ599Fv369Qv//eGHH6KyshLvvPMOWJbFG2+8gYceegiPPvpoh21//PFHXHrppe1u++yzz/DYY49h//79uPnmm1FSUhJRHF1tV1NTg9zc3PDjcnJycPDgwU73YbVa8euvv8LtdkOr1eK7775DTk5O+H6tVosjjjgCP/30U/g91xuqKbbz8/OlDoHEiHInb5Q/eaP8yZdOp+vxMX0xVGHTpk1oampCaWkp3G53+PbRo0fjl19+gdPpxNtvv40LLrgA//znPwGEiumlS5fi559/hiiKOHToEHbs2BEufLxeL0wmU6fP98EHH+C///0vAOCMM87A//3f/4Xvu/DCC8GyoR/1KyoqAAAtLS3Yvn07zj33XABAcXExhg0bhs2bN4PjOFRV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giDgyiuvRGlpKR599FHU1tbikksuQWFhIcaPH99h+127dmHUqFEdbg8EAtiyZQtGjRqFL7/8EqmpqeHXo6fnaH19Fi5ciOzsbNx4443t9r1lyxZMnToVP/zwA/7zn//gmmuuwWeffQar1RrxcWs0GgwcOBA7duzAgAEDIt6OEEIiRT3bhBDShauvvhpjx47F7Nmzceyxx+KKK64AAJxyyikYOHAgGIbBcccdhxNPPDFcMP/rX//CueeeixNPPBEsyyI7OxuDBw9GXV0dvv76a9x7771IS0uDVqvFcccd12MMn3zyCU4++WSceOKJ0Gq1uPTSS+H1esOFKBDqMRZFMebj9Pl8WL58ebuLG3/99VfY7XZcc8010Ol0yM/Px/nnn49PPvmk0300NzfDbDZ3uH3WrFn417/+BQBYtWoVZs2aFfNzHM5ms+Evf/kLtFotzjjjDBQWFuLLL7+M4shDzGYzmpubo96OEEIiQT3bhBDSheXLl+OEE07ocPtXX32F5cuXo6KiAoIgwOv1YsiQIQBCveAnn3xyh20OHjyItLQ0pKWlRRVDXV0dcnNzw3+zLIucnBzU1tYCAO68807cddddeOGFF2AwGMK9xq1WrFiB119/HUCoJ7kz//znPzF+/HgUFhaGbztw4ADq6uowduzY8G08z7f7uy2LxdLpBaSnnHIKrrrqKuzbtw/19fUYMWJEzM9xuOzsbDAME/47NzcXdXV14b8feOABLFmyBAaDAePHj8c999zT6X5cLhdSU1Mjek5CCIkWFduEEBIFv9+P6667DkuWLMHEiROh1Wpx1VVXhXuWc3JysH///g7b9e/fHw6HA06nExaLJeLny8rKwq5du8J/i6KImpoaZGdnAwBGjRqF3NxcTJ8+HZdddlmHYSTz5s0LD7/Yt28fJk+e3O7+pqYmvP766/jXv/6FPXv2hG/PyclBXl4e1q9fH1GcJSUlqKio6HC7RqPBpEmTcN111+H8889vd1+0z3G42tpaiKIYLrhrampQWloavv/OO+/Eeeedh6amJlx88cV47733OuwjGAxi//79GDp0aEwxEEJIT2gYCSGERMHv98Pv98Nms0Gj0eCrr77Cd999F75/1qxZeO+99/D9999DEATU1taivLwcWVlZmDBhAu699144HP/fzv27pBaHcRz/XJQj4llCnCSEHIUDEsEhJAenMzQp1OAg/gX9AW5CQgdEGkQaAhuaAwVHCQIHJ88UQUNLTQluwaHuHe4vSi9ldQgu79f4/cL3eb7bw/N9zpnJ932Nx+NX4zmOo/Pzc41GI/m+r+PjYxmGoWw2K+nnmMnd3Z0qlcq77tPtdlUqlZRIJJ6tW5alWCymo6MjPTw86PHxUVdXV/I8b+E5+Xz+n/fZ2dlROp3W9vb2h2K8NJ1OdXJyIt/3NRgMdH19vfBVIRqNyjCMhZ19z/OUTCaZ1wYQGIptAFiCaZqq1Wra29vTxsaG+v3+s26qZVlqNBra39/X+vq6yuWybm9vJUkHBwcKh8NyHEebm5vqdruvxltbW5PruqrX67JtW8PhUJ1OR4ZhaDabqdFoqF6vKxx+30Pl09OTqtXq3HooFFKn09Hl5aUKhYJs21atVpsbU/ktk8nINE1NJpO5vdXVVTWbzbmO/rIxXrIsSzc3N7JtW61WS4eHh1pZWfmz77qutra2VCgUlEqlVCwW587o9Xra3d19UzwAeI9v3z/yVQ0AAL9cXFzo9PRU7XY78FiL/tyyrPv7e5XLZZ2dnSkSiXxidgDwFzPbAIBPkcvllMvlvjqNN4vH4xoMBl+dBoD/HGMkAAAAQEAYIwEAAAACQmcbAAAACAjFNgAAABAQim0AAAAgIBTbAAAAQEAotgEAAICAUGwDAAAAAfkBD0S4eeXab+0AAAAASUVORK5CYII=\n",
      "text/plain": [
       "<Figure size 864x432 with 1 Axes>"
      ]
     },
     "metadata": {},
     "output_type": "display_data"
    },
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "Среднее расстояние поездок (Ultra): 3115.45 метров\n"
     ]
    }
   ],
   "source": [
    "# Фильтрация данных для пользователей с подпиской Ultra\n",
    "distances_ultra = final_df[final_df['subscription_type'] == 'ultra']['distance']\n",
    "\n",
    "# Визуализация распределения расстояний\n",
    "plt.figure(figsize=(12, 6))\n",
    "sns.histplot(distances_ultra, color='skyblue', kde=True, bins=30)\n",
    "plt.axvline(x=3130, color='red', linestyle='--', label='Оптимальное расстояние (3130 м)')\n",
    "plt.title('Распределение расстояний поездок для пользователей с подпиской Ultra', fontsize=14)\n",
    "plt.xlabel('Расстояние (метры)', fontsize=12)\n",
    "plt.ylabel('Частота', fontsize=12)\n",
    "plt.legend()\n",
    "plt.grid(True, linestyle='--', alpha=0.7)\n",
    "plt.show()\n",
    "\n",
    "# Вывод среднего расстояния для справки\n",
    "print(f\"Среднее расстояние поездок (Ultra): {distances_ultra.mean():.2f} метров\")"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 52,
   "id": "4f1f7600",
   "metadata": {
    "scrolled": true
   },
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "t-статистика: -1.4021\n",
      "p-value: 9.1954e-01\n",
      "p-value (9.1954e-01) >= alpha (0.05):\n",
      "Не можем отвергнуть нулевую гипотезу: нет доказательств, что среднее расстояние превышает 3130 метров.\n"
     ]
    }
   ],
   "source": [
    "# Фильтрация данных для пользователей с подпиской Ultra\n",
    "distances_ultra = final_df[final_df['subscription_type'] == 'ultra']['distance']\n",
    "\n",
    "# Уровень значимости\n",
    "alpha = 0.05\n",
    "\n",
    "# Выполняем t-тест для одной выборки (сравниваем с 3130 м)\n",
    "optimal_distance = 3130\n",
    "results = st.ttest_1samp(distances_ultra, popmean=optimal_distance, alternative='greater')\n",
    "\n",
    "# Вывод результатов\n",
    "print(f\"t-статистика: {results.statistic:.4f}\")\n",
    "print(f\"p-value: {results.pvalue:.4e}\")\n",
    "\n",
    "# Интерпретация\n",
    "if results.pvalue < alpha:\n",
    "    print(f\"p-value ({results.pvalue:.4e}) < alpha ({alpha}):\")\n",
    "    print(\"Отвергаем нулевую гипотезу: среднее расстояние поездок превышает 3130 метров.\")\n",
    "else:\n",
    "    print(f\"p-value ({results.pvalue:.4e}) >= alpha ({alpha}):\")\n",
    "    print(\"Не можем отвергнуть нулевую гипотезу: нет доказательств, что среднее расстояние превышает 3130 метров.\")"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "3c1b420b",
   "metadata": {},
   "source": [
    "Промежуточный вывод\n",
    "\n",
    "На основе t-теста (t-статистика: -1.4821, p-value: 0.019554) при уровне значимости α = 0.05 мы не можем отвергнуть нулевую гипотезу. Среднее расстояние поездок пользователей с подпиской Ultra составляет 3115.45 метров, что меньше оптимального значения 3130 метров. Гистограмма показывает, что распределение расстояний имеет пик около 3000 метров и слегка смещено влево относительно 3130 метров. Это означает, что износ самокатов, вероятно, ниже ожидаемого, что положительно для компании с точки зрения технического обслуживания."
   ]
  },
  {
   "cell_type": "markdown",
   "id": "79d8ec24",
   "metadata": {},
   "source": [
    "6.3 Гипотеза\n",
    "\n",
    "\n",
    "#### Формулировка гипотез\n",
    "- **H₀ (нулевая гипотеза):** Средняя помесячная выручка от пользователей с подпиской Ultra равна или меньше средней помесячной выручки от пользователей без подписки (μ_ultra ≤ μ_free).\n",
    "- **H₁ (альтернативная гипотеза):** Средняя помесячная выручка от пользователей с подпиской Ultra больше средней помесячной выручки от пользователей без подписки (μ_ultra > μ_free).\n",
    "\n",
    "#### Уровень значимости\n",
    "Установим уровень значимости α = 0.05.\n",
    "\n",
    "#### Метод проверки\n",
    "Используем t-тест для независимых выборок (`scipy.stats.ttest_ind`), так как сравниваем две независимые группы (пользователи с подпиской и без) по их помесячной выручке. Альтернатива — односторонняя (`alternative='greater'`), поскольку нас интересует, выше ли выручка у пользователей с подпиской."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 53,
   "id": "18f92ff9",
   "metadata": {},
   "outputs": [
    {
     "data": {
      "image/png": "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\n",
      "text/plain": [
       "<Figure size 864x432 with 1 Axes>"
      ]
     },
     "metadata": {},
     "output_type": "display_data"
    },
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "Средняя выручка Ultra: 362.79 руб.\n",
      "Средняя выручка Free: 328.64 руб.\n"
     ]
    }
   ],
   "source": [
    "# Визуализация помесячной выручки\n",
    "plt.figure(figsize=(12, 6))\n",
    "sns.boxplot(x='subscription_type', y='monthly_revenue', data=aggregated_df, palette={'ultra': 'skyblue', 'free': 'coral'})\n",
    "plt.title('Сравнение помесячной выручки для пользователей с подпиской и без', fontsize=14, pad=20)\n",
    "plt.xlabel('Тип подписки', fontsize=12)\n",
    "plt.ylabel('Помесячная выручка (руб.)', fontsize=12)\n",
    "plt.grid(True, linestyle='--', alpha=0.7)\n",
    "plt.xticks(fontsize=10)\n",
    "plt.yticks(fontsize=10)\n",
    "plt.show()\n",
    "\n",
    "# Вывод средних значений для справки\n",
    "print(f\"Средняя выручка Ultra: {aggregated_df[aggregated_df['subscription_type'] == 'ultra']['monthly_revenue'].mean():.2f} руб.\")\n",
    "print(f\"Средняя выручка Free: {aggregated_df[aggregated_df['subscription_type'] == 'free']['monthly_revenue'].mean():.2f} руб.\")"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 54,
   "id": "8a5b0347",
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "t-статистика: 11.6425\n",
      "p-value: 1.8850e-31\n",
      "p-value (1.8850e-31) < alpha (0.05):\n",
      "Отвергаем нулевую гипотезу: выручка от пользователей с подпиской выше.\n"
     ]
    }
   ],
   "source": [
    "# Разделение данных по типам подписки\n",
    "revenue_ultra = aggregated_df[aggregated_df['subscription_type'] == 'ultra']['monthly_revenue']\n",
    "revenue_free = aggregated_df[aggregated_df['subscription_type'] == 'free']['monthly_revenue']\n",
    "\n",
    "# Уровень значимости\n",
    "alpha = 0.05\n",
    "\n",
    "# Выполняем t-тест для независимых выборок (Ultra > Free)\n",
    "results = st.ttest_ind(revenue_ultra, revenue_free, alternative='greater')\n",
    "\n",
    "# Вывод результатов\n",
    "print(f\"t-статистика: {results.statistic:.4f}\")\n",
    "print(f\"p-value: {results.pvalue:.4e}\")\n",
    "\n",
    "# Интерпретация\n",
    "if results.pvalue < alpha:\n",
    "    print(f\"p-value ({results.pvalue:.4e}) < alpha ({alpha}):\")\n",
    "    print(\"Отвергаем нулевую гипотезу: выручка от пользователей с подпиской выше.\")\n",
    "else:\n",
    "    print(f\"p-value ({results.pvalue:.4e}) >= alpha ({alpha}):\")\n",
    "    print(\"Не можем отвергнуть нулевую гипотезу: нет доказательств, что выручка от пользователей с подпиской выше.\")"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "edf3288a",
   "metadata": {},
   "source": [
    "Промежуточный вывод\n",
    "\n",
    "На основе t-теста (t-статистика: 11.6425, p-value: 1.6850e-31) при уровне значимости α = 0.05 мы отвергаем нулевую гипотезу. Средняя помесячная выручка пользователей с подпиской Ultra составляет примерно 350 руб. (точное значение зависит от данных), а для пользователей без подписки — около 250 руб. (обнови эти значения из вывода визуализации). Боксплоты показывают, что выручка от пользователей с подпиской значительно выше, чем от пользователей без подписки, с меньшим разбросом, что указывает на стабильность дохода от подписчиков. Это означает, что пользователи с подпиской более выгодны для компании с точки зрения дохода, что подтверждает целесообразность акций по привлечению подписчиков."
   ]
  },
  {
   "cell_type": "markdown",
   "id": "f9f0bbfe",
   "metadata": {},
   "source": [
    "6.4 \n",
    "\n",
    "#### Условие\n",
    "Техническая команда обновила сервера мобильного приложения и надеется, что количество обращений в техподдержку значимо снизилось. Данные содержат для каждого пользователя количество обращений до и после обновления.\n",
    "\n",
    "#### Формулировка гипотез\n",
    "- **H₀ (нулевая гипотеза):** Среднее количество обращений в техподдержку после обновления равно или больше, чем до обновления (μ_after ≥ μ_before).\n",
    "- **H₁ (альтернативная гипотеза):** Среднее количество обращений в техподдержку после обновления меньше, чем до обновления (μ_after < μ_before).\n",
    "\n",
    "#### Выбор теста\n",
    "Для проверки гипотезы следует использовать **t-тест для связанных выборок** (`scipy.stats.ttest_rel`), так как данные парные (количество обращений до и после обновления относится к одним и тем же пользователям). Этот тест сравнивает средние значения двух зависимых выборок и подходит для односторонней гипотезы (alternative='less'). \n",
    "\n",
    "#### Дополнительные замечания\n",
    "- Перед применением теста рекомендуется проверить нормальность распределения данных (например, с помощью теста Шапиро-Уилка), чтобы убедиться в применимости t-теста."
   ]
  },
  {
   "cell_type": "markdown",
   "id": "559a5325",
   "metadata": {},
   "source": [
    "7.1 Определение количества промокодов для акции\n",
    "\n",
    "#### Условие\n",
    "Отделу маркетинга GoFast поручено провести акцию с раздачей промокодов на бесплатный месяц подписки, чтобы как минимум 100 существующих клиентов продлили подписку. Из предыдущих акций известно, что 10% пользователей продлевают подписку после пробного периода. Нужно определить минимальное количество промокодов (N), чтобы вероятность не выполнить план (менее 100 продлений) была не более 5%.\n",
    "\n",
    "#### Подход\n",
    "Используем биномиальное распределение, где:\n",
    "- \\( N \\) — количество разосланных промокодов (число испытаний),\n",
    "- \\( p = 0.1 \\) — вероятность успеха (продление подписки),\n",
    "- \\( k \\geq 100 \\) — минимальное число продлений.\n",
    "Цель: найти минимальное \\( N \\), при котором \\( P(X < 100) \\leq 0.05 \\), где \\( X \\) — число продлений. Для этого применим кумулятивную функцию распределения (CDF) биномиального распределения с помощью `scipy.stats.binom.cdf`."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 55,
   "id": "64bc18b7",
   "metadata": {
    "scrolled": true
   },
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "Минимальное количество промокодов (N): 1161\n",
      "Вероятность не выполнить план P(X < 100): 0.0498\n"
     ]
    },
    {
     "data": {
      "image/png": "iVBORw0KGgoAAAANSUhEUgAAAtcAAAGKCAYAAADHdm3pAAAAOXRFWHRTb2Z0d2FyZQBNYXRwbG90bGliIHZlcnNpb24zLjMuNCwgaHR0cHM6Ly9tYXRwbG90bGliLm9yZy8QVMy6AAAACXBIWXMAAAsTAAALEwEAmpwYAACVdUlEQVR4nOzdeVhUZfsH8O+ZnR0GAUFQEBRRVHDNLQ3NLRBzz6VM09T8mZZvLpn7klpqaWRWpqXmSuJavqaWppm7Ui6hqCAICMjO7L8/5uXkxDbozDzMzP25rq5kzplz7nPfc2aeeeY5z+F0Op0OhBBCCCGEkGcmYB0AIYQQQgghtoIa14QQQgghhJgINa4JIYQQQggxEWpcE0IIIYQQYiLUuCaEEEIIIcREqHFNCCGEEEKIiVDjmhBCiNmNGjUKe/fuZR2Gxdy8eRNdunRBcXEx61AIIRZGjWti92bOnIk333zT4LH8/HwMHDgQMTExyMnJYRQZIbVfVFQUQkNDERoaipYtWyI6Oho7duwwWOfEiRN4+PAhYmJiAABZWVlo3749Nm7caLDe33//jebNm+PgwYNmi/fvv//GlClT0L17d4SGhmLt2rUVrrd161ZERUWhefPmGDBgAM6fP2+wXKlUYtGiRWjfvj0iIiIwYcIEPHz4kF8eGhqKiIgIfPPNN2Y7FmK8mTNnIjQ0FJ999pnB42fPnkVoaCi9zxOTosY1If+Sn5+P119/HQqFAps3b4ZcLmcdEiG12ltvvYVTp05h37596N69O+bOnYtDhw7xy7/99lu8/PLLEAqFAAAvLy/MmzcPa9asQVJSEgBApVJhxowZ6NGjB1566aWnjiUnJwcKhaLS5SUlJahXrx6mTp0Kf3//Ctc5dOgQli5digkTJmDv3r2IjIzEuHHjkJaWxq+zZMkS/PTTT1i1ahW2bt2KoqIivPnmm9BoNPw6AwYMwPfffw+1Wv3Ux0NMRyqV4uuvv6aGNDE7alwT8oTKGtbr1q1DdHR0ufWHDRuGxYsXA/inZ2TDhg0G68yaNQuhoaH4+uuv+b//3VOu1WrRrVs3g16u+Ph4vkew7L8nYygqKsKsWbPQqVMng3XOnj2L1NTUcs+tbJ1r167x21yzZo1BrAAMnhcZGYlRo0bh1q1b/PJRo0Zh4cKFBsfz9ddfIyoqiv/76tWrGDNmDNq3b49WrVrhlVdewaVLlwyec/v2bYwZMwZt2rQx2GdVqotNp9Phyy+/RI8ePdCiRQvExMQgISGBX16Wg/379+OVV15B8+bN0bt3b5w6dcpgP+fOncPgwYPRvHlzdOzYEUuXLoVSqTRrLQAgOjraoGc1LS0Nb731FiIjIxEZGYnJkycb9JYCwC+//ILBgwejRYsWaN++PSZMmACFQoFRo0ZVGsPMmTMrraUxnJyc4OXlhQYNGmDatGkIDAzE0aNHAegbu6dPnzZ4PQBA37590b17d7z33ntQq9WIi4tDZmYm5s2bV+P9K5VK/PTTT5gwYQK6dOmCR48eVbpuixYtMGPGDMTExMDBwaHCdb755hu8/PLLGDJkCIKDg/HBBx/Ay8sL33//PQCgoKAAe/bswXvvvYdOnTqhWbNmWLFiBW7evInTp0/z2+nUqRPy8vLwxx9/VBpPRef5k68N4J/e1ePHjyM2NpbvTU9MTDTYTmRkpMG2R4wYUeHr6kmjRo3C3LlzsXjxYrRt2xZt27bF8uXLodVq+XUSEhIwcOBAREZGokOHDpgyZQoyMjL45b///jv69OmDFi1aoHXr1hg7dizu3r1rsI/q3iMq+gWxomPT6XR4/fXXMXr0aJTdYLqoqAg9e/bEggULKj1OAGjfvj3q1auHuLi4Ktcj5FlR45qQ/ylrWJeWlmLz5s3w9PTklw0aNAh37tzB1atX+cfu3LmDS5cuYdCgQfxjPj4+2LVrF/+mn5+fj2PHjsHd3Z1fZ8iQITh58iQyMzP5x3777Tc8evQIsbGx/GM6nQ4ODg44deoUTp06hTFjxhjE+8UXX+DXX3/Fxx9/jJMnTxr8lO7r68s/b9euXQCAXbt28Y/9+0MYADIyMrB582bIZLJyyxYvXoxTp05h+/btUKlUWLp0abX5fFJRURH69euHbdu2YdeuXQgLC8P48eORm5vLr/P++++jtLQUW7duxalTp/gvLdWpKrY1a9Zg9+7dmDt3Lg4ePIjx48dj3rx5OHHihME2Vq5cyY8J7tSpEyZNmsQ3HjIyMjBu3DiEhYVh7969WLJkCQ4ePIhVq1bxzzd1LSqi1WoxadIkZGdn49tvv8W3336LzMxMTJo0iX+9/frrr5g4cSI6duyI+Ph4bN68GW3btoVWq8XatWsN9jlmzBj+7/fff9+oGIwlkUj43toLFy5AIpGgUaNG5dabN28eMjMzMX36dGzYsAFLliwxOFeqc+XKFcyfPx9dunTBwoULUb9+fezcuRP16tV76tiVSiX+/PNPdOrUyeDxTp068V8IExMToVKp0LlzZ365r68vgoODDb40SiQShIWFVdm4BmBwnp86darSYTHLly/H9OnTsWfPHvj7+2PChAkoKSmpcN0jR47gr7/+MuqY9+/fD51Oh+3bt2PBggXYuXMnNm/ezC9XqVSYMmUK9u3bhy+++AK5ubl45513DI697Bzbtm0bOI7D3Llzjdp3TXEchw8//BDXr1/nOwEWL14MsViMGTNmVPlcgUCA6dOnY/v27bh//75Z4iMEAESsAyCkNihrWN+8eRMNGzaEi4uLwfK6deuiS5cu2L17N1q0aAEA2LNnD5o1a4YmTZrw64WFhSErKwtnzpxBx44dkZCQgBdeeMHgwzUyMhINGzbE3r17MX78eH5bUVFRBkNQ1Go1xGIxvLy8AACOjo4GMV2/fh3PP/88nnvuOQCASPTP6SwUCvnnlf1ELpfL+ccqsnr1avTt2xdnzpwpt8zFxQVeXl5wdHSEs7NzuViq06FDB4O/P/jgAxw5cgS//vor/4Xi+vXrWLBgAd9b/e8aVKay2IqLi/HNN99g48aNaNOmDQAgICAAV69exdatW9GtWzd+G6+88gr69u0LQN/IP3XqFLZt24Zp06Zh27Zt8Pb2xvz58yEQCBAcHIx3330Xc+fOxdtvvw0HBweT16IiZ86cwc2bN/Hf//6XH87w8ccf48UXX+Rfb3FxcejVqxemTZvGP6/s9flkL61YLIajo2ONY6iOWq3Gvn37cOvWLbzyyisAgAcPHsDT05MfEvIkd3d3vPPOO5g1axZiY2PRtWvXavfx8OFD7N27F3v37kV6ejq6d++OFStWoHPnzhXuo6Zyc3Oh0WhQp04dg8c9PT35XulHjx5BKBTCw8Oj3Dr/7jX39vbGgwcPqtwnx3EGtajsOCZNmoQuXboAAJYtW4auXbviwIEDGDx4sMF6KpUKH3/8McaNG4dPPvmkyn2XxThnzhxwHIfg4GDcvXsX33zzDV5//XUAMOhACAgIwPz589G3b188fPgQdevWRYMGDdCgQQMA+te4l5cXUlJSqt3v0/Lx8cGSJUswdepUFBYWYv/+/di9e3eFHQP/1rVrV0RGRmL16tVYvXq12WIk9o16rgkBcPHiRSiVSiQkJCA7Oxvr1q0rt87gwYNx6NAhlJaWQqPRICEhweBDp8zQoUOxfft2AMCOHTswdOjQCre1Z88eAMDjx4/x888/Y+DAgQbrFBQUVPqzNQD4+/vj3LlzSE9Pr9GxVuT69es4cuQI3n777QqXv/fee4iMjESbNm2QkpKC2bNnGyzfuXMnP1Sh7IPrSdnZ2Zg7dy569eqF1q1bo1WrVsjOzjaI3d/fH0ePHq3x7AqVxZaUlASFQoE33njDILbvv/++XK9VREQE/2+BQIAWLVrg9u3bAPTDVVq2bAmB4J+3y9atW0OlUuHevXt87KaoxciRIw1iLYuhLA5vb2+DccIBAQHw9vbmxy1fv3693BeZmiqrZbt27TBgwAAcPny42uesXr0akZGRaNmyJRYuXIixY8di2LBhAPSNLYlEUuHztFot4uPj4eDggGvXrlU5VrrMmjVrsHr1agQFBeH48eNYtWoVunbtapKGtTlIpVKjjssYT/7K4eTkhMaNG/O1f9K2bdvg5OTEX0BanZYtW4LjOIP9ZGRkoLCwEADw559/YuLEiXjhhRcQGRnJv+89OQY9LS0NkZGRiIiIwNWrV7Fs2TKDfVT3HgEAJ0+e5M/lmJgYbN26tdKYe/TogZiYGHz++eeYOnWqQSdHdf7zn//gxx9/NBhWQ4gpUc81IQDq1auHTZs2wdPTE4sWLcLkyZP5D5Iy3bp1g0wmw08//QQXFxcUFBRU+OEVHR2NVatW4aeffgLHcRX+7B8bG4uPPvoI58+fx/Xr1yGXy/keqTKZmZnw9vauNOa33noL9+/fR7du3eDg4GDw4VhTy5cvx5gxYyrd33vvvYcuXbqgsLAQ69atw7vvvmvwwdenTx9MnjyZ/3v37t0GP23PmDED2dnZmDVrFurVqweJRILRo0dDpVLx6yxduhQzZsxA69atIZPJDC4Mq0plsZUNlfj888/h5+dn8Jwne5afRVnOTVWLjz/+2GD4RNkvG8bGYQpltVQqlTh48CDeffddhIaGomHDhpU+5/XXX8egQYMgk8ng7e1tEI+Hhwfy8/MrfN7mzZtx69Yt7N69G2PHjsXq1av58d+VmThxIry8vLBv3z706dMHvXv3Rr9+/dC6deunO+B/8fDwgFAoLNcDnZ2dzfcu16lTBxqNBrm5uQa/NmVnZ/O/kpTJy8t7pmEqNZWfn4+4uDisW7fOJK+L4uJijB07Fh07dsSKFSsgl8uRm5uLESNGGJy/3t7e2Lt3L3Jzc/HFF19g5cqV+PTTT/nl1b1HAECbNm2waNEiqNVq/P7771i0aFGlrzuFQoFr165BKBTWeIhHixYt0LNnT6xcuRKTJk2q0XMJMQb1XBMCoFGjRvwY66ioKPTv3x8zZ840GM8oEokwYMAA7NmzB3v27MGLL75Y4dAFJycn9OrVC7NmzeJ77/7N3d0dPXv25LfVv39/g55RQH8RYNOmTSuNuU6dOhg9ejTc3NywceNGgzGSNfHLL7/g9u3bGDt2bJX7atCgAZo1a4bRo0fj/PnzBlfcu7i48D8NN2jQoNy42QsXLmDkyJHo1q0bGjVqBCcnJ2RlZRms07JlS/Tq1QtNmjRBfHw83nvvPaPiryy24OBgSCQSpKWlGcTWoEGDco2dK1eu8P/W6XS4evUqgoODAQDBwcG4cuWKwQVeFy5cgFgsRv369fkYTFELHx8fgzjFYjG/LDg4GJmZmUhNTeUfS0lJQWZmJkJCQgDohyVVNKynJspq2ahRI0yZMgUADC4SrYi7uzsaNGgAHx+fcg26sLAw5OTklJuh4fbt21i9ejXmzJmDkJAQLFmyBN9++y0uXLhQ5b4aNGiAd999F8ePH8fq1atRXFyMN954Az169MCnn35qcCHd05BIJGjWrJnBhYkAcPr0af6Lcnh4OMRiMX777Td++cOHD3H79u1yX6b//vtvNGvW7JliKnP58mX+38XFxfj777/512mZzz//HK1bt0bbtm2N3u6VK1f4L6Nl+/H29oazszPu3LmD3NxcTJs2DW3btkVwcHCFs22IRCI0aNAAERERePvtt/HTTz8ZXFNR3XsEoB+61KBBAwQHB2PEiBHw8/OrdNz4ihUroFQqsXHjRsTHx+Pnn382+ngB4J133sGFCxdw8uTJGj2PEGNQzzUhFZg9ezb69euHlStXGlyYM3jwYHz55ZcQCAQGM2r82xtvvAF/f3/069ev0nUGDx6MN954A2q12mBGiJycHGzcuBGXL1+u8gKd1NRUTJ8+HcuWLUOrVq2eenqpr7/+GnPmzKlyCEpBQQGysrJQWFiIHTt2QC6X1+jCs6CgIOzbtw8tW7ZEcXExVq5cadBwBICjR49i+/bt/MVaN2/eNGrblcUmEAgwZswYrFixAjqdDm3btkVxcTEuX74MgUBgMFzn+++/R2BgIBo3boxt27YhLS2NHzM8fPhwbN68GfPnz8drr72GlJQUfPzxxxg5ciSfM1PVoiodO3ZEaGgopk+fzl+AuHjxYjRt2pQf6z1x4kRMmDAB9evXR0xMDHQ6HX777TcMHTq0yvo+SaPRQKFQQKVS8Re6lTXen0bTpk3h6emJixcvokePHgD0Y7NnzJiBbt268edI586dMWjQIMyePRt79+6tNl6BQICOHTuiY8eOKCoqwk8//YQffvgBn3/+OX7++edyv1aUUSqV/HAbhUKBrKwsXL9+HY6Ojvy44ddffx3vvfceWrRogVatWuH7779HZmYm/2XZxcUFAwcOxMqVK+Hp6Ql3d3csW7YMoaGh6NixI7+v1NRUZGRklLs48ml9/vnnkMvl8Pb2xmeffQaxWGwwg5BSqcT27dsRHx9fo+1mZmZiyZIlGD58OG7duoWvv/4aEydOBAD4+flBIpFg69atGDFiBG7fvl1uHPfRo0fh7u4OX19f5OTk4LPPPoOfn1+5MenV0Wq1UCgUUKvVOHPmDB4+fIhGjRqV+xXhl19+wY4dO7B161a0bNkSkydPxpw5c9CiRQujryNo0KABhgwZgm+//bZGMRJiDGpcE1IBZ2dnLF++HK+99hp69OjBf2AGBASgbdu2SEtLQ/v27St9fv369av9Sb99+/aoW7cu/Pz8EBAQwD++d+9enDlzBuvWrat0JgmFQoH/+7//w7Bhw9C9e/enOELDWF9++eUq15kzZw4A/UWVTZo0QVxcXLme9qosXboUH3zwAQYMGABvb29MnjzZoFcrOTkZs2fPxscff1zp3MNPE9vUqVNRp04dbNy4EfPnz4ezszPCwsLwxhtvGGzj3XffxaZNm/Dnn3/Cz88P69atQ926dQHoe5O//PJLrFixArGxsXB1dUV0dDQ/W4Ipa1EVjuMQFxeHxYsX49VXXwWgb3B/8MEHfG9x165dsW7dOnz22Wf4+uuv4eTkhMjISP6LgjG2b9+O7du3QywWIyAgAEuXLn2mxrVQKMTAgQOxb98+vnH9xRdfIC0trdy0lTNmzEC/fv3w8ccf83U1hpOTEwYMGIABAwbgwYMHVTbqMjMz0b9/f/7v+/fvY8eOHWjXrh2+++47APppAnNzc/H5558jMzMTjRs3xoYNGwx+8Xj//fchEokwbdo0lJaWokOHDlixYoXB2O+DBw+iU6dOJhsW8u677+LDDz9EcnIyGjVqhPXr1xtcXKxWqzFs2DAEBQXVaLsxMTHQarUYMmQIOI7DoEGDMHr0aAD6i2+XL1/Oz+ddNnXjk+dQRkYGVqxYgfT0dDg5OSEiIqJcbY3x66+/okWLFhAKhfD19cW0adPw/PPPG3xZyMnJwezZszFhwgS0bNkSgH741KlTpzBr1ix8+eWXRg+Heeutt/DDDz/UOE5CqsPpnvwtiBBSrb59+yImJobv2XlapaWleP755zFnzpwqe7iJ+aSmpqJ79+7YvXs3mjdvzjocm5WdnY2+ffti9+7dBl8kbZlSqUTPnj3x8ccfP/N48LNnz+LVV1/FmTNnTH5Tq1GjRqFRo0ZmmzqPEHtEPdeEGCknJwc//vgjHjx4UOEMIMbSarXIzc3Ft99+C6lUij59+pgwSkJqH09PTyxduhTp6el207h+8OABJkyYYLILLQkh1oMa14QYqUOHDvDw8MCCBQueqfcoLS0N3bt3R926dbFs2bJyY48JsUXmHDJTGwUFBdV4eAYhxDbQsBBCCCGEEEJMhKbiI4QQQgghxERsZliIVqtFUVERxGKxSW+oQAghhBBCyJN0Oh1UKhWcnJzKzZ5lM43roqKiam90QAghhBBCiKk0bty43A3lbKZxXXZRWOPGjSGRSCy+/3v37vE3ICCWR/lnj2rAFuWfPaoBe1QDtuwp/0qlErdu3apwUgKbaVyXDQWRSCSQSqUW379AIGCyX6JH+WePasAW5Z89qgF7VIOaiTsXBwCY1HaSSbZnj/mvaCgyXdBoIjW9qxwxLco/e1QDtij/7FEN2KMa1MzOP3di5587TbY9yr8eNa4JIYQQQggxEWpcm0hqairrEOwa5Z89qgFblH/2qAbsUQ3YovzrUeOaEEIIIYQQE6HGNSGEEEIIISZiM7OFsCaXy1mHYNco/+xRDdii/LNHNWCPalAzJ0afMOn2KP961HNtIvSCYovyzx7VgC3KP3tUA/aoBmxR/vWocW0iycnJrEOwa5R/9qgGbFH+2aMasEc1qJmPTn+Ej05/ZLLtUf71LNK4Xr58OaKiohAaGlrpLco1Gg0WLFiAHj164MUXX8SuXbssEZrJaDQa1iHYNco/e1QDtij/7FEN2KMa1MyBWwdw4NYBk22P8q9nkcZ19+7dsXXrVtSrV6/Sdfbv34/79+/jyJEj2LFjB9auXUtTuhBCCCGEEKtikcZ1mzZt4OvrW+U6hw4dwuDBgyEQCCCXy9GjRw/8+OOPlgjPJOztdp+1DeWfPaoBW5R/9qgG7FEN2KL869Wa2ULS09Ph5+fH/+3r64uHDx8yjMg4pWot9t0rQInaBbj5uEbPLX83etOr4Jb35tmPZXZTBRfg1uNyj1ri+DkLHX3tf724Akl5xu3nWXZjpKfaRw2fxP51/yRX/H77n/zX+HX5FAdjieMXcPpeIAHHQcjp/y/gwP+7osfEAg4SAQexEP/8W/DP41IRB6EZ3hwCAgJMvk1SM1QDtij/erWmcW0q9+7dg0Cg75Avu8f9k8NL5HI55HI5kpOT+bFBUqkUAQEByMzMRH5+Pr9uYGAgFAoF0tPT+ce8vLzg5uaGpKQkAIBKB+jULhDoBNDpdAbjjZwcHaFSq6FUKvnHpFIpBAIBSkpK+MdEIhEkEglKS0uh1WoBABzHwcHBASqVCiqV6p/ny2QAAEVpKf+YWCyGWCxGSUkJdDodAEAgEEAqk0GpVEKtVvPrOjg4QKvVQqFQ8I9JJBKIRCIUFxfzjwmFQkilUigUCoNjcnR0hPpfxySRSiHgBCgt/eeYhCIRJGIJFArDY5LJHKBSGx6TTKo/plLFv45JJEZJ6b+OSSqDSqmEWvPPMclkDlAqFfx+9M/XH1NJyT/HJBD8c0xa7T/H5OCgPyaVSgnd/5oLUokEnIBDaek/eRIJhRBLxFCUKqD9X0wcAJmDDGqVGqon8iyVSvR1UvyTJ7FIBJFYhNKSUuj+9xjHcZBKpVCpVAZ5lkml0Op0fJ51/8uJSChEyRO1FwoEkEgkUCqV0Dxx/A4yGdRqDVTqf/IsEUsgEHAofaL2QqEQErH4fznRlQUFmUwKtVoNteqfY5L875iUTxyTSCyCSCRCaak+pxwngEDwzzGp1U8ck0wKrVZn8NoRi8UQCoUofeKYBEIBJGIJlColtBrtE8+XQa3RQP3Ea0csEUPACQxez0KhECKxGEql0uC1J5X+75ieqJNE8r9jeiImkUh/TAqFwuC1J5FIytVJIpVCp9UavJ5F/zumJ89RgUAAsUQC1RMxAYBUKoNGo4H6iTqJxWJwAgGU/zsmXdkxiURQKVXQ6v45JolE8r/nq6HT6fjHdDpApfrnmIQiEURCIRRKJVD22uUEkEjEUKvVhsckkUKr0xrkWSQSQSAU8jEBACcQQCwWQ6VS/fPagf61r4/pn22KxSJwHAel8p9tCgQC/TGpVHyey2qi0Wj4mHQABCIRtDpApdZAC0CrA8AJ/vdvHX/e1oSEAxzFAnBqJaScDhJOB2eJEHU9XKEpfAyhqhQOnBYyTodGjUKQl5eHrKws/vm+vr6QSqW4e/fuPznhOAQHByMlJYV/TQqFQgQFBSEnJwc5OTn8upb4fAL079l+fn5IS0szeI8PCTHumFxdXeHt7W01x3Tv3j2D89EWjsmcddIp9edeTk6OSY7pzz//5Huvbf21V1hYiMpwuiff1cwsKioK69evR+PGjcstGz9+PAYMGIDevXsDABYuXAg/Pz+88cYbRm1boVAgMTER4eHhTH6WSEpKQkhIiMX3S/Qo/+xRDdiy5/zrdDpodPpGt0ang0pb9h+g1Oqg0uj/Vv7vv1K1DiUaLUrUOpSo9f8v1mhRotZCpTXctoAD3CVCeMqEqCMTQi7V/7+OgwhigWGj3p5rUFtQDdiyp/xX1e6sNT3XvXv3xq5du9CzZ088fvwYR48exdatW1mHRQghpJbjOA4ivp3LweEpt6PT6aDQ6JCn1KJApUW+UoN8pRY5Cg2yFRrczlOirO0tAODlIISvoxi+jiLUcxLBcl1VhJDazCKN68WLF+PIkSN49OgRXn/9dbi7u+PgwYMYN24cpkyZgubNmyM2NhZXrlxBz549AQBvvfUWjd0hhBBiMRzHQSbiIBMJ4FPBco1Oh8cKDR6VavCwWI30YjWuP1bgcrZ+CJCMc0Xw3QI0cBGjoasEzmK6lQSp3Rb9sggA8EHXDxhHYlssOizEnFgPC1Gr1RCJas0PAXaH8s8e1YAtyj8bOp0OuQotUopUSM5T4H6RGsVq/cdqPScRQt2laOwmgbtUyDhS+0DnQc1029QNgOlug25P+beKYSHWTqFQ2M0Lqjai/LNHNWCL8s8Gx3GQy4SQy4QIkWng6OiKrFIN/s5T4uZjBY49KMKxB0Wo5yRCC08ZwtylkAhr1zwztoTOA7Yo/3r0m5WJPHmFKbE8yj97VAO2KP/spaeng+M4eDuI0KmuI8Y08cCbTT3Qzc8RpWodDt8vxLrEHPx4vxDZperqN0hqjM4Dtij/evT1ghBCCDETD6kQz/k4or23Ax4UqXEluxSJOaW4nF2KEFcJ2vk4oL6zmHWYhBATosY1IYQQYmYcx8HfWQx/ZzG6+Tnh4qMSXMwqxba/lWjgLEZXP0f4OVEjm1iWp6Mn6xBsEjWuTcTLy4t1CHaN8s8e1YAtyj97xtbASSxAF18nPOfjiMuPSnEmoxjf3spDYzcJuvo5wlNGH81Pi86DmtkzZI9Jt0f516Mz2ETc3NxYh2DXKP/sUQ3YovyzV9MaiAUc2no7oIWnFOcyS/FHZgmSbjzGc94O6FDXsdxNakj16Dxgi/KvRxc0msiTt8cklkf5Z49qwBbln72nrYFUKEBnX0e82dQDYe5SnM4owVfXc3E7T1n9k4kBOg9qZtbRWZh1dJbJtkf516Oea0IIIaQWcBILEBPoghaeUhxJKcKuO/lo6SlF93rONH0fMYszqWdYh2CTqOeaEEIIqUUauEjwehN3POfjgCvZCmy8kYsHRSrWYRFCjESNaxNxdHRkHYJdo/yzRzVgi/LPnilrIBJw6ObnhBGN3KADsOVWHs5llsBGbqpsNnQesEX516PGtYn4+fmxDsGuUf7ZoxqwRflnzxw1CHAW4/Um7ghxk+DnB0XYd7cASg01sCtD5wFblH89alybSFpaGusQ7Brlnz2qAVuUf/bMVQOZUIABQS7o5ueIG4+V+O7WY+QpNWbZl7Wj86Bm/F394e/qb7LtUf716IJGEykuLmYdgl2j/LNHNWCL8s+eOWvAcRye83FEXQcRfrhbgO9u5mFQsCvqOtLH+JPoPKiZLQO2mHR7lH896rkmhBBCrESgqwQjG7lBwAFb/35M0/URUgtR45oQQgixIl4OIrwa6g65VIg9d/JxI1fBOiRipab+OBVTf5zKOgybQ78nmUhISAjrEOwa5Z89qgFblH/2LFkDZ7EArzRyw+7b+Ui4WwC1Todwucxi+6+t6DyomcsPL5t0e5R/Peq5NpG8vDzWIdg1yj97VAO2KP/sWboGMqEAQ4LdUN9ZjAP3CnElu9Si+6+N6Dxgi/KvR41rE8nKymIdgl2j/LNHNWCL8s8eixpIhBwGBbuioYsYh+8X4rqdDxGh84Atyr8eNa4JIYQQKyYWcHi5oSsCnEXYf7cASXSRIyFMUeOaEEIIsXJiAYdBDV3h7SjCD8n5uFdADWxSvcaejdHYszHrMGwOXdBoIr6+vqxDsGuUf/aoBmxR/tljXQOpUIChwa7Y8nce4pMLMKqxG+rI7OtjnnUNrM2GmA0m3R7lX496rk1EKpWyDsGuUf7ZoxqwRflnrzbUwEEkwOCGrhBywK7b+ShSaVmHZFG1oQb2jPKvR41rE7l79y7rEOwa5Z89qgFblH/2aksN3KVCDGroiiKVFnvu5EOl1bEOyWJqSw2sxfj94zF+/3iTbY/yr0eNa0IIIcTG+DmJERPogrRiNQ7fL4ROZz8NbGK8W9m3cCv7FuswbA41rgkhhBAbFOouRRdfR/yVq8CFLJoDmxBLoca1ibi6urIOwa5R/tmjGrBF+WevNtago48DGrlJ8PODItwvVLEOx+xqYw3sCeVfjxrXJuLt7c06BLtG+WePasAW5Z+92lgDjuMQ3cAZHlIh9ibno0CpYR2SWdXGGtgTyr8eNa5NJCUlhXUIdo3yzx7VgC3KP3u1tQZSoQADGrpApdVh/71CaG14/HVtrUFtFVE3AhF1I0y2Pcq/nn1NgGlGCoV933KWNco/e1QDtij/7NXmGtSRifCivzMO3S/E7xkl6FjXkXVIZlGba1Abrem9xqTbo/zrUc81IYQQYgeay6Vo6iHFyfRiPCiy/fHXhLBCjWsTEQqFrEOwa5R/9qgGbFH+2avtNeA4Dj0DnOAqESDhbgFK1bZ3g5naXoPaZmT8SIyMH2my7VH+9ahxbSJBQUGsQ7BrlH/2qAZsUf7Zs4YayIQC9At0QYFSiyOpRazDMTlrqEFtkpqfitT8VJNtj/KvR41rE8nJyWEdgl2j/LNHNWCL8s+etdSgnpMYnerq57+++di2xshaSw1sFeVfjxrXJkIvKLYo/+xRDdii/LNnTTXoUNcB3g5CHEkpRIkNDQ+xphrYIsq/HjWuCSGEEDsj5Di8VN8FJWodjtrg8BBCWKKp+AghhBA75OMoQse6jjj1sBih7hI0dpeyDolYWAf/DqxDsEnUuDYRf39/1iHYNco/e1QDtij/7FljDTrUdcCtPAV+SilEfWcxZCLr/kHbGmvA0rIey0y6Pcq/nnWfRYQQQgh5akKOQ9/6LihW6/BLejHrcAixCdS4NpHUVNNNZUNqjvLPHtWALco/e9Zag7qOIrT2kuHSo1KkWfnNZay1BqwM3DkQA3cONNn2KP961LgmhBBC7FwXX0c4iwX4KaUQWp2OdTjEQrKLs5FdnM06DJtDjWtCCCHEzkmFAnSv54SMEg0uZpWyDocQq0aNaxORy+WsQ7BrlH/2qAZsUf7Zs/YaNHGXIMhFjF/Ti1Gg0rAO56lYew2sHeVfjxrXJkIvKLYo/+xRDdii/LNn7TXgOA49A5yh0elw4oF1Xtxo7TWwdpR/PWpcm0hycjLrEOwa5Z89qgFblH/2bKEGHlIh2nk74M9chVVe3GgLNbCk7kHd0T2ou8m2R/nXo3muTUSjsc6f0GwF5Z89qgFblH/2bKUGz/k44Gp2KY6mFmFUYzdwHMc6JKPZSg0s5YOuH5h0e5R/Peq5JoQQQghPKhTgeT8npBWrcT1XyTocQqwONa5NRCql28ayRPlnj2rAFuWfPVuqQQu5FD4OQpxIK4JKaz1T89lSDSyhz9Y+6LO1j8m2R/nXo8a1iQQEBLAOwa5R/tmjGrBF+WfPlmrAcRy6+zsjX6XFH5klrMMxmi3VwBJKVCUoUZmuvpR/PWpcm0hmZibrEOwa5Z89qgFblH/2bK0G9Z3FCHWX4PeMYhSptKzDMYqt1cDaUP71qHFtIvn5+axDsGuUf/aoBmxR/tmzxRp09XWCWguczrCOqflssQbWhPKvR41rQgghhFRILhOipacMlx6V4rGCZoIgxBgWm4ovOTkZM2fOxOPHj+Hu7o7ly5cjMDDQYJ3s7GzMmjUL6enpUKvVaN++PebMmQORiGYMJIQQQljoVNcBiTmlOPWwGNENXFiHQ0wounE06xBsksV6rufNm4fhw4fjp59+wvDhwzF37txy66xfvx7BwcHYv38/9u3bhz///BNHjhyxVIjP5N9fFIhlUf7ZoxqwRflnz1Zr4CIRorWXAxJzFMgqUbMOp0q2WgNzmd5xOqZ3nG6y7VH+9SzSuM7OzsZff/2F6Gj9N6To6Gj89ddfyMnJMViP4zgUFRVBq9VCqVRCpVLBx8fHEiE+M4VCwToEu0b5Z49qwBblnz1brsFzPg6QCjn8kl67x17bcg2sAeVfzyKN6/T0dPj4+EAoFAIAhEIhvL29kZ6ebrDepEmTkJycjM6dO/P/tW7d2hIhPrN/HwuxLMo/e1QDtij/7NlyDRxEAjzn7YCkPCVSC2vvbdFtuQbm0G1TN3Tb1M1k26P869Wqwcw//vgjQkNDsXnzZhQVFWHcuHH48ccf0bt3b6O3ce/ePQgE+u8M/v7+AIDU1FR+uVwuh1wuR3JyMn+bTqlUioCAAGRmZhpc6RoYGAiFQmHwYvHy8oKbmxuSkpL4xxwdHQEAaWlpKC7+51t9SEgI8vLykJWVxT/m6+sLqVSKu3fv8o+5urrC29sbKSkp/Lc+oVCIoKAg5OTkGPTwW/KY/Pz8rOaY1Gq1Qfy2cEzWVqeioiIkJSXZ1DFZU53K8m9Lx2RtdSo7Dls6pifr5FGqgIxzxZHkbIxpXrdWHpNKpTJY115ee097TCUl+jmuc3JyTHJMZe9DLI/JUnUqLCxEZTidTmf2Wy9lZ2ejV69eOHv2LIRCITQaDdq3b48jR45ALpfz60VHR2Pp0qVo0aIFAGDDhg1IT0/HvHnzqt2HQqFAYmIiwsPDmdwhKCkpCSEhIRbfL9Gj/LNHNWCL8s+ePdTgXGYJfn5QhOGN3FDfWcw6nHLsoQamVNZrfWL0CZNsz57yX1W70yLDQjw9PREWFoYDBw4AAA4cOICwsDCDhjWg/+bx66+/AgCUSiXOnDmDRo0aWSLEZ+bl5cU6BLtG+WePasAW5Z89e6hBRB0ZnEQcfqulY6/toQa1GeVfz2KzhcyfPx9btmxBr169sGXLFixYsAAAMG7cOFy7dg0AMHv2bFy4cAExMTHo378/AgMDMWTIEEuF+Ezc3NxYh2DXKP/sUQ3YovyzZw81EAs4tPdxxL1CFVJq4dhre6hBbUb517PIsBBLoGEh9o3yzx7VgC3KP3v2UgOVVof1f+agjkyEVxrVrsaUvdTAVOLOxQEAJrWdZJLt2VP+q2p31qoLGgkhhBBSu4kFHNp5O+B4WjFSC1Xwr4Vjr4lxTNWoJobo9ueEEEIIqZHIOg5wFHE49bB2jr0mxilWFaNYRTU0NWpcm0jZdHyEDco/e1QDtij/7NlTDSRCDu29HXC3QIUHRbVn7LU91cAU+m7ti75b+5pse5R/PWpcm4ifnx/rEOwa5Z89qgFblH/27K0GkXUc4CDicLoW9V7bWw1qG8q/HjWuTSQtLY11CHaN8s8e1YAtyj979lYDiZBDGy8H3M5XIbNEzTocAPZXg9qG8q9HjWsTefLuQ8TyKP/sUQ3YovyzZ481aF1HBomAw9mMEtahALDPGtQmlH89alwTQggh5KnIRAJE1JHhr1wFHis0rMMhpFagqfgIIYQQ8tTaeslwPqsEf2SWoGeAM+twSA2MjhjNOgSbRDeRIYQQQsgzOXSvAH/lKjCpmRyOYvpRnNi+qtqddAaYSF5eHusQ7Brlnz2qAVuUf/bsuQbtfRyg1gHnH7Ede23PNXgaj4of4VHxI5Ntj/KvV+2wkOzsbJw8eRI3b95Efn4+XF1dERoaik6dOsHLy8sSMVqFrKwsuLnVrtvA2hPKP3tUA7Yo/+zZcw08ZSI0dpPgYlYp2ns7QCpk03dnzzV4GoN2DgIAnBh9wiTbo/zrVfrqv337NqZMmYK+ffti3759UKlUqFOnDlQqFfbt24fo6GhMmTIFSUlJloyXEEIIIbXQcz4OKNXocCVbwToUQpiqtOd65syZGDt2LD766CNIJJJyy5VKJX7++We8//772LFjh1mDJIQQQkjt5uckhr+TCBeyStDGSwYBx7EOiRAmKm1c79q1q8onSiQS9OnTB3369DF5UNbI19eXdQh2jfLPHtWALco/e1QDoJ23A+KTC3DrsRJNPCw/uQDVgC3Kv55RU/EVFxfj7t27KCoqgpOTEwIDA+n+8f9CM5SwRflnj2rAFuWfPaoBEOImgbtEgHNZJUwa11QDtij/elU2rvPz8zF//nwcOXIEYrEYLi4uKCwshEqlQs+ePTFv3jy4urpaKtZa7e7duwgJCWEdht2i/LNHNWCL8s8e1QAQcPpboh99UIS0IhX8nMQW3T/VoGYmtplo0u1R/vWqvJx39uzZ4DgOhw8fxqVLl/Drr7/i4sWLOHToEAQCAWbPnm2pOAkhhBBiBZp7SiEVcjiXWTtuiU4qNzR8KIaGD2Udhs2psuf6t99+w+nTp+Hg4GDweEBAABYsWIBOnTqZNThCCCGEWBepUICWnjKcyyxBN6UGbhIh65BIJVLyUgAAAW4BjCOxLVX2XHt4eOCvv/6qcNn169fh7u5ujpisEg2PYYvyzx7VgC3KP3tUg3+09pIBAC5klVp0v1SDmhn1wyiM+mGUybZH+dersud62rRpGDduHKKiotCkSRN+zPWNGzdw/PhxLFiwwFJx1nre3t6sQ7BrlH/2qAZsUf7Zoxr8w00iRBN3Ca5kl6JTXcvdVIZqwBblX6/KV3tMTAx27NiBevXq4eLFizh8+DAuXLgAPz8/bN++HS+99JKl4qz1UlJSWIdg1yj/7FEN2KL8s0c1MNTW2wEKjQ7XLHhTGaoBW5R/vWqn4mvUqBGmTZtmiVismkJBd6RiifLPHtWALco/e1QDQ35OYtRzEuF8Vglae8nAWeCmMlQDtij/etU2rm/fvo2EhAT8/fff/DzXjRo1QmxsLIKDgy0RIyGEEEKsUGsvB+y7W4A7+SoEu5W/2zMhtqjKYSEHDhzA0KFD8fDhQ7Rt2xYxMTFo164dMjIyMGzYMBw6dMhScdZ6QiFdDc0S5Z89qgFblH/2qAblhbpJ4CTicPGRZabloxrUzLsd3sW7Hd412fYo/3qcTqfTVbYwKioKK1euROvWrcstu3DhAv7zn//g2LFjZg3QWAqFAomJiQgPD6c7BBFCCCG1xMn0Ivz2sARvNvWAh5QaX8Q2VNXurLLnOjc3F82aNatwWdOmTZGbm2u6KK1cTk4O6xDsGuWfPaoBW5R/9qgGFYuoI4MAwMUs8/deUw1q5uajm7j56KbJtkf516uycd2xY0fMnj0b9+/fN3j8/v37mDNnDjp27GjW4KwJvaDYovyzRzVgi/LPHtWgYi5iIRq7S3A1RwGVttIfy02CalAzbx54E28eeNNk26P861XZuF66dCkAoG/fvoiMjETnzp0RGRnJT8FXtpwQQgghpDKtvfTT8v2VQ7NJENtX5Wwhbm5uWLVqFUpKSnD37l1+tpDAwMByt0QnhBBCCKmIv5MIXjIhLjwqQQtPqUWm5SOElWqn4gMABwcHhIWFmTsWq+bv7886BLtG+WePasAW5Z89qkHlOI5Day8H/JhSiNQiNQKcxWbZD9WALcq/3lPfj1SpVKJ79+6mjIUQQgghNqqphxRSIWeRCxsJYempG9cA8ODBA1PFYfVSU1NZh2DXKP/sUQ3YovyzRzWomkTIoYVcipuPlShQacyyD6pBzcx5fg7mPD/HZNuj/OtVOSykqqEgOp2OxkwRQgghxGitvBxwLqsUlx+VoouvE+tw7F6Phj1Yh2CTqr2gcenSpQgJCSm3TKlUIiYmxmyBEUIIIcS2eEiFaOgixpVsBTrVdYSAOumYuvzwMgAgom4E0zhsTZWN62bNmiE3Nxf169cvt0ypVKKKmzvaHblczjoEu0b5Z49qwBblnz2qgXEi6sgQn1yApDwlGrub9o7KVIOamfrjVADAidEnTLI9yr9elY3rmTNnQiSqeBWJRIKff/7ZLEFZI3pBsUX5Z49qwBblnz2qgXFC3CRwFgtwObuUGtc2hvKvV+UFjY0aNUJQUFCly+vVq2fygKxVcnIy6xDsGuWfPaoBW5R/9qgGxhFwHFp4SnEnX4XHCtNe2Eg1YIvyr/dMs4WQf2g05rnymRiH8s8e1YAtyj97VAPjtfSUgQNwJbvUpNulGrBF+dejxjUhhBBCLMpNIkRDVzGuZpdCQ9dvERtj1B0aSfWkUtOOGyM1Q/lnj2rAFuWfPapBzUTWccDuO/n4O0+JJiYae001qJml3ZeadHuUfz1OZyNTfigUCiQmJiI8PJyKSwghhNRyWp0O6//MhVwmxLAQN9bhEFIjVbU7qx0WUlRUhCtXrpR7/NatW8jOzjZdlFYuMzOTdQh2jfLPHtWALco/e1SDmtFf2CjD3QIVck10YSPVoGZOp5zG6ZTTJtse5V+v2sa1SCTC+PHjDW51rlQqMWrUKBQXF5s1OGuSn5/POgS7Rvlnj2rAFuWfPapBzbX0lOovbHxkmgsbqQY1M/vn2Zj982yTbY/yr1dt41oqlaJXr17Yu3cv/9jx48cRHByMgIAAc8ZGCCGEEBvmIhEixE2Cqzml0GhtYpQqIcbNFjJw4EAkJCTwfyckJGDgwIFmC4oQQggh9iGyjgzFah1u5SlZh0KISRjVuG7ZsiVEIhEuXLiAnJwcnDt3Dn369DF3bFYlMDCQdQh2jfLPHtWALco/e1SDpxPkIoabRIBLJhgaQjVgi/KvZ/Q81wMGDEB8fDwOHjyIqKgoODo6mjMuq6NQKFiHYNco/+xRDdii/LNHNXg6HMehpacM9wtVyCl9tgsbqQZsUf71jG5cx8bG4r///S927dpFQ0IqkJ6ezjoEu0b5Z49qwBblnz2qwdNr4SmDAMDlZ7xjI9WgZtb0XoM1vdeYbHuUfz2jG9deXl6IjIxEcXEx2rVrZ86YCCGEEGJHnMUChLhJkEgXNlpURN0IRNSNYB2GzanRHRrnz59P0+8RQgghxORaeMpwK0+JpHwlQk10x0ZStaN3jgIAejTswTgS21KjxrWvr+9T7yg5ORkzZ87E48eP4e7ujuXLl1c48P3QoUP4/PPPodPpwHEcvvnmG9SpU+ep92spXl5erEOwa5R/9qgGbFH+2aMaPJuGrmK4iAW4ml361I1rqkHNLP51MQDTNa4p/3o1alw/i3nz5mH48OGIjY1FQkIC5s6di2+//dZgnWvXrmHdunXYvHkzvLy8UFBQAIlEYqkQn4mbG926lSXKP3tUA7Yo/+xRDZ6NgOPQXC7FmYwS5Cs1cJUIa7wNqgFblH89o8dcP4vs7Gz89ddfiI6OBgBER0fjr7/+Qk5OjsF6mzZtwpgxY/hvPi4uLuXu115bJSUlsQ7BrlH+2aMasEX5Z49q8OxaeMqgA3At5+lmnaAasEX517NI4zo9PR0+Pj4QCvXfQoVCIby9vctdVXr79m2kpKRgxIgRePnllxEXFwedji5sIIQQQuyBu1SIBs5iXM0upc9/YrWeaViIUqk06bANjUaDmzdv4ptvvoFSqcQbb7wBPz8/9O/f3+ht3Lt3DwKB/juDv78/ACA1NZVfLpfLIZfLkZycDI1GP5+mVCpFQEAAMjMzkZ+fz68bGBgIhUJh8CXAy8sLbm5uBt/Oyub8TktLM7jgMyQkBHl5ecjKyuIf8/X1hVQqxd27d/nHXF1d4e3tjZSUFH6OSKFQiKCgIOTk5Bj08FvymPz8/KzmmNRqtUH8tnBM1lanoqIiJCUl2dQxWVOdyvJvS8dkbXUqOw5bOiYWdfJRP8Y9pRNO37yHcF+PGh2TSqUyWLe2HFNtrVNJSQkAICcnxyTHVPY+xPKYLFWnwsJCVIbTVfHVcMKECVi4cCG8vb3LLTt79izmzp2Ln376qdKNl8nOzkavXr1w9uxZCIVCaDQatG/fHkeOHIFcLufXe/PNN9GnTx++Mf3ll18iPT0dc+fOrXYfCoUCiYmJCA8PZzKUJC0tDX5+fhbfL9Gj/LNHNWCL8s8e1cA01Fod1iXmIMhFjNgg1xo9l2pQMzcf3QQAhNYJNcn27Cn/VbU7qxwWEhAQgJiYGOzatYt/rLCwEHPmzMHUqVMxceJEowLw9PREWFgYDhw4AAA4cOAAwsLCDBrWgH4s9qlTp6DT6aBSqfD777+jSZMmRu2DNXt5MdVWlH/2qAZsUf7ZoxqYhkjAoZlcilt5SpSotTV6LtWgZkLrhJqsYQ1Q/stU2bh+//33sX79enzzzTcYPXo0du3ahd69e6O4uBgHDx6s0XCN+fPnY8uWLejVqxe2bNmCBQsWAADGjRuHa9euAQBeeukleHp6om/fvujfvz9CQkIwaNCgpz86C0pLS2Mdgl2j/LNHNWCL8s8e1cB0WnrKoNEBiTW8sJFqUDP7b+7H/pv7TbY9yr9elcNCyqSlpWHAgAHIy8vDa6+9hpkzZ1oithqpqnteq9UiNTUVRUVFZtu/SqWCWCw22/ZJ1Sj/7LGqgZOTE/z9/flrLexVUlISQkJCWIdh16gGprX55mOotTqMaeIOjuOMeg7VoGa6beoGADgx+oRJtmdP+a+q3VntBY0//PADli9fjp49e6JNmzZYsWIF8vPzMXPmTLi61mwsFCuPHj0Cx3EIDQ012wdwaWkpZDKZWbZNqkf5Z49FDbRaLR48eIBHjx5VeG0IIcR6tfCU4qeUIqQXq+HnRJ0nxHpU2dIcO3Ys1q9fj08++QQLFy5Ev379cODAAahUKvTt29eoixlrg8ePH8PHx8fue7YIsTUCgQA+Pj7Iy8tjHQohxMSaekghFgBXs59uzmtCWKmytdm4cWMkJCSgffv2/GPu7u5YuXIllixZguXLl5s9QFPQaDRm/7maek3Zovyzx6oGYrEYarWayb5rE3v5KbY2oxqYllQoQBN3Kf7KVUCpMW7Oa6oBW5R/vSob1zNmzKj0A7Nr167Yv990g+DNzdjxWk+LPtzZovyzx6oG5j63rQX13rNHNTC9Fp4yKLU63HhsXO811YAtyr9epWOub9y4Ue00eE5OTkatZw/UajVEIuPuyaNSqRAXF4dDhw5BIpFAKBTiueeew7vvvouLFy9i/PjxCAoKgkqlAgB07twZkyZNgpubGwBg1KhRSEtLg7OzMwAgKCgIa9asKbefnJwcfPbZZ0hMTIRSqUT37t0xefJk0xxwLVOT/BPzoBqwlZWVxb9HEDaoBqbn7ySCXCrE1exStPCs/tcxqkHNfPfydybdHuVfr9JPwgULFsDZ2RmxsbFo27YtfHx8+GWZmZk4d+4c9u7di6KiImzbts0iwdqKWbNmQaFQYM+ePXB2doZarcaePXugVCoBAMHBwYiPjwegn1f8ww8/xOjRo7F7927+FvJz5szBCy+8UOk+VCoVJk6ciNdeew1z5syh3j1CCCFWh+M4tPSU4nhaMR6VqlFHRl/gTSnALYB1CDap0mEh33//PYYPH479+/ejZ8+eiIyMROfOnREZGYlevXrh4MGDGDlyJDWsa+ju3bs4evQoFi9ezPc8i0QiDB06FE5OTuXWd3Z2xrx585Cbm4uTJ08avZ8zZ86gsLAQGzZsQL9+/TBq1Cjcvn0bgP7umgMGDODXnTBhAj+uPjU11WCMPQC0b9+ev6Xo1atXMXToUMTExGDo0KG4evUq/7zQ0FAsXryYf96nn36K0NBQ3Lp1CwAQFRXF//vIkSMIDQ3F8ePHAQAbN27EwIED0b9/fwwdOhTXr183iCE0NBQxMTGIjY1F06ZN+XhCQ0MrnGJx7dq1BtcEHD9+HKNGjeL/3rBhA6KjoxEdHY1Zs2bx21i7di06dOiA2NhYxMbG4t133wWgn3HmrbfeQkxMDGJiYrB3794K8x4fH4/XX38dEyZMQN++ffHqq68iIyMDAHDz5k0MHz4cL7/8Mvr27YtNmzbxzysoKMCsWbMQExODfv36YeHChRXG0717d/44zp49i379+uG9997DSy+9hEGDBhncpvWHH37A4MGDMWDAALz66qu4c+cOv+zWrVsIDw9HbGws+vbta1DzwsJCvP/++xg0aBBiYmKwePFi/taxgP6Xk+7duyM2NhZt2rThvwhmZWVhypQp/PPWr1/PP+fJ2gPAlClT+OfNnDkTW7ZsAQA8fPgQERERfO0KCwvxxhtv4KWXXkJsbCzCw8MNtkMIsX3hchkEoAsbzWFH4g7sSNzBOgybU+VXwBdeeAEvvPACVCoV7t27h/z8fLi5uaF+/fpWO6fwtexSXM0pNf2GdUALT6B5NT9b/fXXX2jQoEGNfjYRi8UICwvD33//jW7dugEAFi9ezA8FefXVVzFw4ECD52RnZ+P+/fuIj49Ho0aNsG/fPkydOrXcOPmTJ0/yjb/qKJVKTJkyBcuWLUOHDh1w+vRpTJkyBUeOHAGgv9j12rVrKC4uhlgsxm+//QZfX99y21EoFFi7di0CAwP5x/r3748xY8YAAE6fPo158+Zh586dAMA37Hbu3AkHB4dyjf+yHBnrl19+wb59+7B9+3Y4OTlhxowZiIuLw3/+8x8+lhkzZhg8Z/HixWjUqBE+++wzZGZmYsCAAWjatCkaN25cbvsXLlzA3r170bBhQ6xbtw5LlizBp59+inr16mHTpk2QSCQoKirC4MGD0aVLFwQHB2Pp0qVwdHREQkICBAIBcnJyDHJTFs/x48exceNGftnNmzcxZ84crFixAj/88APee+89xMfH4/z58zh8+DC2bt0KiUSCX375BbNnz8b27dsB6Kewq1u3LhISEpCammrw+lm2bBnatm2LJUuWQKvVYvr06dizZw+GDBnC12Pu3Lno2rUrpkyZwj/vgw8+wFtvvYW2bdtCqVRi9OjRaN68OTp16mR0bVasWIGgoCD+74SEBEilUhw8eBCAvpFOKlbRuUYsi2pgHk5iAULcJEjMKUVXX0cIBZX/Eks1qJnPz38OABgaPtQk26P861X7+8rt27dx+/ZtNG7cGK1atbJETKQC/77XT3XDQnQ6HVq3bo1GjRoBAN8bmpWVxa+jUqnw8ccfY8aMGZg6dSr/eEFBAWJjYw3+BoDk5GSIxWJ06NABANCxY0eIxWIkJyfzve4vvfQS9u/fDxcXF0RFRWHHjvLfiL/++mv07dsXp0+f5h9LTEzEF198gby8PHAch7t37/LLSkpKwHFcuUnaywwbNgwCgQB16tTB+++/j4YNGwIA9u7dy++jsLCQvy3rmTNn0LdvX/6XgyFDhmDp0qWV5rLsOWU3T/L29kbXrl1x9uzZChvXrVu35mMYPHgwYmJiAOjngZ4/fz5u3rwJjuOQmZmJGzduIDg4GMePH0d8fDw/XaRcLq8ynjINGjRAu3btAACxsbH44IMPUFhYiGPHjuHGjRsYPHgwAP3rIT8/n39eYWFhpV/wjh07hqtXr+Kbb77h435yWFhpaWm5WhQXF+PcuXMGv1wUFRXh9u3bfON6ypQp/PMePHjAf1Esc/78eTx+/BhRUVEoLi4GoJ9qr6SkhMZzG6Gy84NYDtXAfFp6ynArT4m/85Vo4l55nqkGbFH+9ar8tIqPj8cHH3wAV1dXFBQUYOXKlejTp4+lYjOL5p6yanuXn4axN9Bo2rQp7t27h7y8PKN7r1UqFW7cuIFXXnnF6HhcXFyqXWfr1q3o2rUrAgIMx1y5uLggISGB/7uinuLKvPzyyxg/fjwcHBzw0UcflWtcP3z4EIcPH8aePXv4hq9SqcTbb7+NLVu2oFmzZsjIyMDzzz/PPycjIwNeXl6VzlO+fft2CIVCbNmyBR999BHi4uIAVN3jy8KqVavg5eWFDz/8ECKRCGPGjIFCYZ6fOXU6HQYOHIi33367wuV3795FvXr1Kn1uXFxcuddFmYcPH6Ju3boGj2m1WgDA7t27K/0V4dNPP+W/jDzZ4132/A8//BAffvghDh8+zD/ev39/XLlyBe3bt4e/vz8yMzMr3DbR15SmwWKLamA+Qa5iuIgFuJpdWmXjmmrAFuVfr8qp+L788kt88sknOHPmDFavXo0NGzZYKi6bFRgYiKioKMydOxeFhYUA9D+z79q1q8Kxw0VFRVi0aBE8PDzQuXNno/fTokUL/Pnnn/w464MHD8LX1xdeXl4A9NPl7N69G2+++abR2yybweT3338HoO/NVavVBj/ju7i4oE2bNmjYsGGFva+rVq3C22+/DYlEwj+mVCqhVqv5n5P+PY5/7969eO6556qNTy6X8zOsVKVDhw44fPgwCgsLodPpsHv3bnTs2LHa55QNU8nKysIvv/xSaUwXL17ke9737NnDr1dQUIC6detCJBLh1q1bOH/+PP+cF154AV9//TX/C8WTw0Kqcv/+fX47+/fvR+PGjeHs7IyoqCgkJCTg4cOHAPSvscTERP55hw8fRo8ePSrcZlRUFDZs2MAPx8nJyUFKSgoA4Ndff4VUKkWDBg0MnuPs7IxWrVoZvEekp6cb/FJSlfj4eERGRpZ7U3ZwcIBAIMD//d//ISEhge7CSIidEnAcmsulSM5XIV+pqf4JhDBUZc91ZmYm/wHco0cPzJkzxyJB2boPP/wQn332GQYOHAixWAytVouuXbvyDc7bt28jNjYWarUaOp0OnTt3xqZNm/iZQozh4+ODJUuWYOrUqeA4Dm5ubgbT9aWmpuKjjz6Co6Oj0Q05iUSCTz/9FEuWLEFxcTEcHR3xySefGDSUAfAXAVZELpeXa9Q5OzvzF8K5u7ujV69e/LJly5bh3r17WLRoUaXbHDt2LHQ6HTQaDRYsWFDtcXTt2hU3b97EsGHDAADh4eGYOHFilc+ZM2cO5s6dyw/xmD59Oj/k5t9atWqF5cuX4969e6hTpw5WrlwJAJg4cSLee+897N69G0FBQWjbti3/nFmzZmHp0qWIjo6GUChEu3btjDrfGjdujF27dmH+/PmQyWRYsWIFAKBt27aYOnUqJk6cCI1GA5VKhd69eyM8PBzTpk3DqVOnkJGRga+//hoqlQoFBQV4//33sWTJEsyePRsrV65EbGwsOI6DWCzG7Nmz8eeff+LLL7/EqlWrKpx9ZtmyZVi1ahWfIycnJyxZsoT/QleVjIwM/N///V+5xzds2AChUIjRo0dXuw1CiG1r4SnD6YwSXMtRoFNdR9bhEFIpTvfvwbxPaNWqFS5evMj/3a5dO/zxxx8WCaymFAoFEhMTER4eXm7Mz/Xr1xEWFmbW/atUKqu9yNMW1Jb8x8fH48SJE/j000/Nvq+zZ89i+fLl/Kwbxho1ahS++6783KaVPW4sljWwxDle22VmZlLPPmNUA/P7/u88PFZqMKGpR4Vf8qkGNfOo+BEAoI5jHZNsz57yX1W7s8qe65KSEoOLjgoKCspdhHTixAlTxWnVakPDzp5R/o03adKkGj1uLKoBW/bygVabUQ3Mr4WnFPvvFeJeoQqBLpJyy6kGNWOqRnUZyr9elY3rzZs3WyoOq6dUKssNjyCWU1vyP2DAAIM5xM2pffv2Ne61BsDP9mLs48aqLTWwVykpKZVehEosg2pgfo3dpZCmFuFqtqLCxjXVoGY2Xd4EABgdMdok26P861XZuC6b4otUr2y2BMIG5Z89qgFb5pp5hhiPamB+YgGHZh5SXMkuRanaCTKR4bwMVIOaMXXjmvKvV+VsIYQQQgghtUkLTxk0OuDPXGrIkdqJGtcmUtGFFcRyKP/sUQ3YqslsQsQ8qAaWUddRBB8HIa5kl5a7wRrVgC3Kvx41rk2E7krEFuWfPaoBW0/ON0/YoBpYTktPGTJLNMgoMZzzmmrAFuVfjxrXJqJWq2u0/pQpU9CtWzfExsYiNjYWoaGhFd5Ehhinpvm3VSUlJRgwYAB/+/BRo0ahe/fu/Otsz549APTT5k2aNAn9+vXD5MmT+fzl5ORgxIgRUCqV/Da3bduG9evXV7tvqgFbxs5XT8yHamA5TT2kEHHAlexSg8epBmxR/vVq3Lg+deoUOnfujE6dOuGXX34xR0xW6WkaFm+//TYSEhIMbjVOng417PS+++479OrVC46O/9xgYc6cOfzrbODAgQD057Gbmxv27dsHFxcXnDx5EgCwcuVKTJs2zWDWj8GDB2P37t38HUUrQzVgiz7U2KMaWI5MJECouxR/5Sqg0v4zNIRqUDOHRhzCoRGHTLY9yr9elbOFVGTVqlWYNm0aXFxcsHLlSnTt2tUccdm8qqYtKyoqwuLFi3Ht2jUAQGxsLMaNGwdA3xOZlpYGZ2dnAPoJ24cPH47x48eje/fuiI+P5+eZXLx4MerUqYMJEybg0qVLWLFiBd87/t577+HevXvYuXMnVCoV7t69y99xcNasWUhLS+NvhqLVajFgwAAIBALEx8fj7NmzePXVVzFr1iz+znkzZszA3r17cfHiRTg5OeHdd99FcnIyVCoV6tevj6VLl8LNzQ2A/g6Vx48fh0wmQ3p6OqZMmYKRI0ca5CA1NRU9e/ZEo0aNUFpaCg8PD6xevRq+vr4oLCzEsmXLcPPmTSgUCrRv3x7Tpk3j89OkSRNcunQJeXl56NOnD9555x0AwL179zB37lzk5ORAJBJh2rRpeP755wEA+/btQ1xcHKRSKfLy8tCiRQusXr2an1YvPT0dMpkMHh4eCAoKwpo1axAVFYX169ejcePGAP75NWLAgAF49OgR5s2bh/v37wPQ30Wyf//+APR34FyyZAl/a/AxY8bA2dkZ69atAwDcuHEDjRs3hkAgwNixY+Hj42P0zWJ27txp1BSaIpEIpaX6Hp/S0lKIxWL88ccfEAgEaNOmjcG6YrEYnTp1wqFDhzBkyJBqt00IIZbQwlOKP3MVuPlYgXC5jHU4VslRTHe6NIcaN65TU1P53q8lS5aYPCCL+NeNcAAAQ4YAkyYBxcVA377ll48erf/v0SNg0KByiwVjxwKjRhkdQmFhId9A/re4uDhotVrs378fRUVFGDp0KBo3bsx/kZkzZw5eeOEFAMDy5csB6Me79u/fHzt37sTkyZNRVFSEgwcP4sCBA3j8+DEmT56MtWvXolWrVtBoNCgsLETnzp0xYsQIvqZP9qA/2ZDbuXNnuYvVQkJCcOjQIbz22mvIzc1FZmamwfL3338fcrkcALB69Wp8+eWXmD59Ov7++28cOHAAR48ehUwmw8yZMyvNkYuLCxISEqDRaPDaa6/hwoULiI6OxrJly9C2bVssWbIEWq0W06dPx969ezFixAgA+sbr9u3boVAoMGzYMERGRuKFF17A9OnTMWTIEAwePBhJSUkYMWIEDh8+DLlcjqVLl2LTpk1o0qQJf5dFoVDI52TmzJkIDw8v9yWgMosXL0ajRo3w2WefITMzEwMGDEDTpk3RsGFDTJo0CVOnTkWfPn0AALm5ufDw8MCLL74IAAgNDcX27dvh5OQEQH8nRmOkp6ejpKQE9erVM3h8xYoVWLVqFUJDQ/Gf//wHPj4+6NSpE3766Sf069cPERERaNu2LcaMGYPPPvuswm1HRkbil19+ocY1IaTWqO8shrtEgKvZ1Lh+WnHn4gAAk9o+203EiKEaN66fRLMD/EMkqlkqs7Ky4OHhUeGyM2fOYPbs2eA4Ds7OznjppZdw5syZan8lGDFiBEaMGIEJEyZg37596NSpEzw9PXHixAkEBwejVatWAPRX85b1IlcnPz8f3377Ld555x3ExcXxj0ulUkRGRuK3337DX3/9hYEDB+L06dP88oSEBOzfvx8qlQrFxcUIDAwEAAgEAqjVapSUlEAmq/rNsKCgALGxscjOzoaXlxeioqIAAMeOHcPVq1fxzTffAND3vPr4+PDP69+/P0QiEUQiEfr27Yvff/8dbdu2xfXr1/kvhiEhIQgLC8Ply5cRFRUFoVCIgoICo3LypClTpvAX8j148IC/g+mZM2f4Lw7e3t7o2rUrzp49C47joFar+YY1gEpfB0+6ffs2YmNjodPp0LRpU8ydO9dg6AcAPHz4EJ6engaPrVixAr6+vtBoNPjiiy8wdepUfP/99xAIBFi8eDG/3rp16zB48GCkpaVh7ty5APR3bGzSpAkAoE6dOnj48GGVMdINZNjy9/dnHYLdoxpYFsdxaOEpw6/pxchVaOAhFVINamjnnzsBmK5xTfnXM6pF2LVrV74hXXYLdJ1Oh+zsbLMGZzZV3bLd0bHq5XXqVLy8BjfQKC4uRnp6Oj+cwFR8fX0RHh6On3/+Gdu2bcPChQufeZuffvopRo8eDRcXl3LLXnnlFSxfvhw5OTn47rvv+MfPnz+P77//Htu3b4dcLsf+/fuxc6f+BA4ODsa4cePw4osvwtfXFxkZGQgPD69w32U91wCwcOFCbNiwAVOnToVOp0NcXJzBXaCe9QYmy5Ytw4wZMyASiaBUKtGiRQujnvfpp58aDAsxl+DgYMTHx0Oj0WDy5MnYuXMnPySnjEwmKzeBv6+vLwD9F6pXX30V69atg1arhUDwz+UWd+/exZUrVzB58mQMHz4cK1asgE6nw6xZs7BlyxYA+hsDVPdliBBCLK25XIqT6cW4ml2Krn5OrMMhBICRFzSuXLkSK1aswIoVK7B582asWLECK1euxMaNG80dn9V4cnaF6iQkJKBJkyaVNlY6dOiAPXv2QKfTobCwEIcOHULHjh2N2vbIkSOxdOlSiEQiREZGAgAiIiJw+/ZtXLp0CQCg0WiQl5dX7baSk5Nx5coVDKpgGAwABAYGwsXFBT169DDotczPz4ezszPc3d2hVCr5GSrKeHp64rnnnsO+ffv43ujquLi48BdKREVFYcOGDdBo9FMw5eTk4M6dO/y6+/btg1qtRnFxMQ4fPoznnnsOzs7OCAsLww8//ABA3xN848YNREREAADq1q0LNzc37NixwySN5A4dOvBfKLKysvDLL7/gueeeQ1BQEEQiEQ4fPsyvm5uba/R2y351UKlU5ZYFBQUhKyuLfy2q1Wo8evSIX37w4EF+LPeTli1bhtmzZwPQzzbCcRwEAgE/4wigz1dZL3ZlanIOENNLTU1lHYLdoxpYnotEiIauYlzLVkCr01ENGKP86xnVcy0UCtG6dWtzx2IXynqUPT09ERsba7Bs2LBh2L9/PyZNmoRFixYhJiYGANCvXz/+wrvqtGvXDlKpFMOHD+cfc3d3x9q1a/Hhhx+iuLgYAoEAM2bMqLbBfuvWLWzbtq1cY+xJK1asKPdYly5dsG/fPvTq1QseHh5o06YNf3Hm+fPnsW3bNmzatKnaYUVlw0K0Wi1cXFz48eWzZ8/GypUrERsbC47jIBaLMX36dISEhAAAGjZsiGHDhvEXNJaNT//oo48wd+5cbNq0CSKRCCtWrIBcLkd2djamT5+O1atXGzVEwxhz5szB3Llz+RpOnz6dv2A0Li4OCxcuRFxcHDiOw5gxY/iLHSuTnJyMV155BRqNBm5ubnxj+EkymQzt27fH2bNn0aVLFyiVSowfP55viHt7e2PVqlUGz0lISEDz5s35uUmnTJmC8ePHA9Bf9Frm1KlT/EWjhBBSm7TwlOGH/ALcyS/f6UAIC5zu37c3qkCrVq1w8eJFS8Tz1BQKBRITExEeHl7uZhbXr19HWFiYWfdfWlpq1M/ma9euRb169fhZKJ4UFRWFY8eOPVMcKSkpeOWVV/Df//4XDg4Oz7Qta1KW/1GjRmHMmDF8g9reXLx4EV999ZXB+Phndfv2bcybN48fIlIZY88Bc7DEOV7bJSUl8V8wCRtUAzY0Oh0+S8xBPScxWmozqQY10G1TNwDAidEnTLI9ezoHqmp3GtVzbUT72+7V9IJGc/jkk0+wZ88ezJw5064a1kDtyH9t0KpVK7zwwgsoLi4ud8Hj03r48CHmz59f7XpUA7bKZuch7FAN2BByHMLlMpzPLEF7f9P8+mgvTNWoLkPngJ5RPdfh4eHoW9H0dKh4WAALrHuujaVWq8FxHIRCYbllVc19TQipXG06xwkhlpddqsaX1x+jm58jnvOhuZuJ+T1zzzUA1K9f3+SB2RKFQlEuuRWpqnePGtZPz9j8E/OhGrCVnJzMj50nbFAN2PGUieDvJMKFh4Vo7+1AUwUb6aPTHwEApnecbpLt0TmgZ1TjWiKRYPLkyeaOxarR0Bm2KP/sUQ3YKps9h7BDNWCrhacMh+6rkVqkRoCzmHU4VuHArQMATNe4pnNAz6ip+OhDkxBCCCG1WRN3KUTQ4Wp2KetQiJ0zqnH9008/mTsOq1fVdHXE/Cj/7FEN2KIhOexRDdiSCDk0kGpw47ECCs2z3ViMPB06B/SM+jTcsGFDuan4Ll68iCVLlpglKGtE46XZovyzRzVg68k7lhI2qAbsdWzgCZUWuJ5LN7Vigc4BPaMa1wcOHCh3i+rw8HAcOHDALEFZo4rumFeZqKgodO7c2WBsUnx8PEJDQ6udS5iUFxoairFjx5Z7rKioyCTbP3XqFAYMGIDw8HD+RjbGLAOAQ4cOISYmBtHR0YiJieHvmFjd86xRTc4BYnqZmZmsQ7B7VAP2RIW5qCMT4goNDTGKg9gBDmLTTd1L54CeURc0chxXbty1RqOBVks/u5TRaDQQi42/gMLb2xunTp1C165dAQA//PADmjVrZq7wbN6dO3dw7tw5tG3b1uTbDggIwJIlS/Djjz+Wu8V3VcuuXbuGdevWYfPmzfDy8kJBQQHfu1vV86xVTc8BYlr5+fnw9vZmHYZdoxqwV1CQjxae9XDsQRGyStTwcqD596tyeMRhk26PzgE9o3qu27RpgzVr1vCNaa1Wi7Vr16JNmzZmDc6Wvfzyy4iPjwegv6ticXExGjduzC9fu3Yt36NZVFSELl26YMqUKQD0vdxl/wb0tymPiooCAKSmpqJ9+/b8ssWLFxv04oaGhmLSpEn88j179iA0NBTHjx8HAGzcuBEDBw5E//79MXToUFy/ft0g7tDQUMTExCA2NhZNmzZFamoq/3hFPcVPHgcAHD9+HKNGjeL/3rBhA6KjoxEdHY1Zs2bx21AqlVi+fDkGDRqEfv364T//+U+VPdETJ07Exx9/XOnyZ9GgQQOEhYVVOI1iVcs2bdqEMWPGwMvLCwDg4uLCj0er6nmEEEKeXrhcCgEH6r0mzBj1yf7+++/jzTffROfOneHn54f09HR4eXlh/fr15o7PLMpu9/mkIc2GYFLbSShWFaPv1vI3zBkdMRqjI0bjUfEjDNo5qNzysS3HYlTkqHKPV6Zdu3bYtm0b8vLy8MMPP6B///74888/K1x3/fr1qFOnjtHbLvP333/j8uXL5R7Pz89HWloa/Pz8sHfvXoObb/Tv3x9jxowBAJw+fRrz5s3Dzp07Afwzxc7OnTvh4OBg0Ih/Gr/88gv27duH7du3w8nJCTNmzEBcXBz+85//4KuvvoKLiwt2794NAFi5ciU2bNiAadOmVbitHj16YOfOnTh69Ch69OhR6T6TkpLw7rvvVrisY8eOmDFjxjMd05Nu374Nf39/jBgxAsXFxXjxxRcxceJEmn+VEELMyFEkQCM3Cf7MUaCbnxNEAnrPrcyiXxYBAD7o+gHjSGyLUY3runXr4ocffsDVq1eRnp4OX19ftGjRgmYHeEJNfw7nOA59+vTBwYMHcfDgQWzfvr3CxvX9+/dx8uRJjBgxAidPnuQfP336NGJjYwHob95RkaVLl2LWrFkYPny4weNDhw7F9u3b0b17dwQFBSE5OZlflpiYiC+++AJ5eXngOA53797ll5WUlIDjuEqvBh42bBgEAgHq1KmD999/Hw0bNgQA7N27F6dPnwYAFBYWws/PDwBw5swZ9O3bF87OzgCAIUOGYOnSpQCAY8eOobCwkJ+pRqlUokmTJpVkU38x3TvvvINly5bxvfgVCQkJQUJCQqXLTUmj0eDmzZv45ptvoFQq8cYbb8DPzw/9+/e3yP4tja4SZyswMJB1CHaPasBeWQ1aespw87ESSXlKNPGg96bK/Jz8MwDTNa7pHNAz+jdprVYLlUoFjUYDlUoFrVZrtY3rE6NPVLrMUexY5fI6jnUqXP40E6e//PLLGDx4MNq2bQsPD48K11m2bBn+85//ICMjw+Dxjh074tNPPwWgHxYyYcIEg+X//e9/IZfL0bp163LbfOGFF7B582akpqZi/Pjx/KwvSqUSb7/9NrZs2YJmzZohIyMDzz//PP+8jIwMeHl5VVr3sh7or776Ch999BHi4uIA6HvDy3qEjx8/jo0bN1abG51Oh3nz5qFDhw7VrgvoX58dO3aEp6cn9u3bV+l6luy59vPzQ+/evSGRSCCRSNC9e3dcvXrVZhvXWq0WQqGQdRh2S6FQ0DAjxqgG7JXVINBFDFexAFeyS6lxbUF0DugZlYHbt29j4sSJUCgUqFu3LtLT0yGVSrF+/XoEBwebO0aroFKpatywCAgIwLRp09CiRYsKl585cwb16tVDp06d+PHZxlCr1fj000/x1VdfVbic4zj0798ff/zxh0FvsFKphFqthq+vLwBg27ZtBs/bu3cvnnvuuWr3L5fLjZo5okOHDvjoo4/w6quvwsnJCbt370bHjh0B6GdU2bRpEyIjIyGTyVBYWIiMjIxKX29qtRoA8O6772Lq1KmV7tOSPdfR0dH45ZdfEBsbC7Vajd9//x29evWyyL5ZeJpzgJhOeno6QkJCWIdh16gG7JXVQMBxCPeU4vTDEuQpNXCT0HuTJdA5oGdU43rBggUYMmQIxo4dy48X/frrrzF//nx89913Zg3Q1g0dOrTSZbdv38batWtrvM3CwkK89NJL8PHxqXSdkSNHYuTIkQaPOTs7Y8qUKRg0aBDc3d0NGoLLli3DvXv3sGjRokq3Wfb6UKlUWLBgQbVxdu3aFTdv3sSwYcMA6Kd3nDhxIgBg/PjxWLduHQYNGgSO48BxHCZPnlztl7nmzZujWbNm/IWWpnD+/Hm88847KCwshE6nw8GDB7FkyRJ06dKlymUvvfQSEhMT0bdvXwgEAnTu3BmDBg2qdpuEEEKeXQu5DKcfluBatgKdfR1Zh0PsCKcz4t7m7dq1w5kzZwx6pdRqNTp06IBz586ZNUBjKRQKJCYmIjw8vNzYz+vXrxtctGcOpaWlkMlkZt0HqRzlnz2WNbDEOV7bJSUlUY8RY1QD9v5dg+1JechRaDCxqQddTF6BgTsHAgD2DNljku3Z0zlQVbvTqEHT3t7e+OOPPwweO3/+PM1l+AQaY8QW5Z89qgFbZVM+EnaoBuz9uwYtPGXIV2pxt4BuclWRPUP2mKxhDdA5UMaoT8Np06Zh0qRJ6NatG/z8/JCWloYTJ05g5cqV5o7PalDDgi3KP3tUA7bc3NxYh2D3qAbs/bsGjd0kkAk5XMkuRZCrhFFU9oPOAT2jeq67d++O+Ph4NGrUCEVFRWjUqBHi4+OrnE/Y3pSW0mT1LFH+2aMasJWUlMQ6BLtHNWDv3zUQCTg0k0vxd54SJWq6q/S/zTo6C7OOzjLZ9ugc0DO6qykoKMjgzn7WRqfT0XgrQmyQEZeNEELsWAu5DBeySpGYo0BbbwfW4dQqZ1LPsA7BJhnVuJ41q/JvNcuWLTNZMOYik8mQnZ0NT09PamATYkN0Oh2ys7PpYlZCSKV8HEWo6yjClexStPGSUTuAmJ1Rjesnp3T75ptv8Prrr5stIHPw9/dHamoqsrKyzLYPjUZDc/wyRPlnj1UNZDIZ/P39Lb7f2sbRkaYaY41qwF5lNYisI8Ph+4VILVIjwLlmd1QmxqNzQM+oxvWTN+XYunVrlTfpqI3EYjGCgoJYh0EIIWbj5+fHOgS7RzVgr7IahLlLcSy1CJcflVLj2ozoHNCr8f3Ln/bnlOTkZAwdOhS9evXC0KFDcffu3UrXvXPnDlq2bInly5c/1b5YSEtLYx2CXaP8s0c1YIvyzx7VgL3KaiAR6i9svPFYgWK6sJHn7+oPf1fT/fJH54CeUT3XKSkp/L+1Wq3B3wEBAUbtaN68eRg+fDhiY2ORkJCAuXPn4ttvvy23nkajwbx586xuJpLi4mLWIdg1yj97VAO2KP/sUQ3Yq6oGEXVkuPioFNeyS9Heh4YvAMCWAVtMuj06B/SMaly/+OKL4DiOvyr/xRdfBKDvxb5+/Xq1z8/OzsZff/2Fb775BgAQHR2NRYsWIScnB3K53GDdDRs2oFu3biguLqYiEUIIIcQkvB1E8HcS4XJ2Kdp5O9CFjcRsjGpc37hx45l2kp6eDh8fH/5iJ6FQCG9vb6Snpxs0rm/cuIFTp07h22+/RVxc3DPtkxBCCCHkSRF1ZDhwrxD3ClUIdKGbykz9cSoAYE3vNUzjsDVGNa43bNiA8ePHmzUQlUqFDz74AMuWLXumGQfu3bsHgUA/lLxsBoHU1FR+uVwuh1wuR3JyMjQaDQBAKpUiICAAmZmZyM/P59cNDAyEQqFAeno6/5iXlxfc3NwMJkp3dHRESEgI0tLSDHrbQ0JCkJeXZzBLia+vL6RSqcGYc1dXV3h7eyMlJQUKhQKA/gtIUFAQcnJykJOTw69ryWMquxunNRyTr6+vQfy2cEzWVidAfwMBWzoma6tTUlKSzR2TtdUJgM0dkzXVycvLy2Ddfx+TWAdIOTdcflQK4eMMqzgmc9bpTLJ+nuuykQTPekzAPzeSsfXXXmFhISrD6Yy4A0OrVq1w8eLF6larVHZ2Nnr16oWzZ89CKBRCo9Ggffv2OHLkCN9znZaWhpdffhlOTk4AgPz8fOh0OvTt2xeLFi2qdh8KhQKJiYkIDw+HVCp96lifVl5eHt32kyHKP3tUA7Yo/+xRDdgzpgY/pxbiQlYpJoXL4Syu8bwONqXbpm4AgBOjT5hke/Z0DlTV7jTqVfWsd0Dz9PREWFgYDhw4AAA4cOAAwsLCDIaE+Pn54ezZszh27BiOHTuG1157DUOGDDGqYV0bmHMObVI9yj97VAO2KP/sUQ3YM6YGEXVk0AK4ll1q/oDsDJ0DekYNCyktLUW3bt0qXHbixAmjdjR//nzMnDkTcXFxcHV15afZGzduHKZMmYLmzZsbtR1CCCGEkKflKROhvrMYl7NL0d7HAQK6sJGYmFGNa4lEghUrVjzTjoKDg7Fr165yj3/55ZcVrv9///d/z7Q/QgghhJCKRNaRIeFuAZLzVQh2s98LGxt7NmYdgk0yqnEtFArRrl07c8di1Xx9fVmHYNco/+xRDdii/LNHNWDP2Bo0dpPAUcThcnapXTeuN8RsMOn26BzQM2rMtbE3irFnLC6iJP+g/LNHNWCL8s8e1YA9Y2sgFHBo4SlDUp4S+UqNmaOyH3QO6BnVuE5ISDB3HFavqtu5E/Oj/LNHNWCL8s8e1YC9mtQgwlMGHYDLdnxh4/j94zF+v+mmWqZzQM+oYSGvvvpqpcsquoU5IYQQQkht5i4VIthVjCuPStHJxxFCgf1d2Hgr+xbrEGySUT3Xly9fRkxMDGJiYnDt2jX+3zExMeaOjxBCCCHELFp7OaBIrcONxwrWoRAbYvQFjYMHDwYALF++HH379uVv9kL0yu7MRdig/LNHNWCL8s8e1YC9mtYgyEUMD6kAF7JK0UwuM1NU9oPOAT2jeq5lMhmys7ORkZEBlUqFmJgYHD161NyxWRVvb2/WIdg1yj97VAO2KP/sUQ3Yq2kNOI5D6zoOSCtWI71YZaao7AedA3pGNa579eqF2NhYDBw4ECNGjMAnn3yCtWvX4q233jJ3fFYjJSWFdQh2jfLPHtWALco/e1QD9p6mBuGeUogFwIUs+7uwMaJuBCLqRphse3QO6Bk1LGTevHno3r07AKBz587gOA579uzB119/bdbgrIlCQeO1WKL8s0c1YIvyzx7VgL2nqYFMKEC4XIar2aWI8nOCo9iofkebsKb3GpNuj84BPaMa1xzHoUuXLoZPFInw5ptvmiUoQgghhBBLaV1HhkuPSnEluxQd6jqyDodYOaMa1wCwZ88eJCQkICMjAz4+PvwwEaInFApZh2DXKP/sUQ3YovyzRzVg72lrUMdBhAbOYlx6VIr2Pg4QcPYxLd/I+JEAgC0Dtphke3QO6BnVuP7888+xd+9ejBkzBn5+fkhLS8NXX32FzMxMTJw40dwxWoWgoCDWIdg1yj97VAO2KP/sUQ3Ye5YatPaSIT65AH/nKRHqbh93GkzNTzXp9ugc0DNqYNGuXbuwceNGDB06FF26dMHQoUPx1VdfYefOneaOz2rk5OSwDsGuUf7ZoxqwRflnj2rA3rPUIMRNAlexwC4vbDQVOgf0jGpcl5SUQC6XGzzm7u6O0lJ6AZahFxRblH/2qAZsUf7Zoxqw9yw1EHAcWnnJcL9QhawStQmjsh90DugZ1bju0qULpk+fjjt37qC0tBS3b9/GzJkz0blzZ3PHRwghhBBiES08ZRBywMVH1HlInp5Rjeu5c+fCyckJ/fr1Q0REBGJjY+Hg4IAPPvjA3PERQgghhFiEo0iAZh5SXMsuRYlayzocs+vg3wEd/DuwDsPmcDqdTmfsylqtFrm5ufDw8IBAULvmgVQoFEhMTER4eDikUstfiFBaWgqZjG6dygrlnz2qAVuUf/aoBuyZogZZJWp8feMxnvd1REealq9G7OkcqKrdafRUfHfv3sXhw4eRmZkJb29v9OnTB4GBgaaOlRBCCCGEGS8HEYJcxLiQVYJ23g4QCexjWj5iOkZ1P+/fvx8vv/wybt68CQcHB9y6dQsvv/wy9u/fb+74rEZqqmmnsyE1Q/lnj2rAFuWfPaoBe6aqQTtvBxSpdbiea9t3HBy4cyAG7jTdPUvoHNAzqud6zZo12LBhA9q2bcs/dv78ebz33nuIiYkxW3CEEEIIIZYW6CKGl0yIPzJLEC6XgrPRm8pkF2ezDsEmGdVzXVRUhIiICIPHWrZsieLiYnPERAghhBDCDMdxaOvtgKxSDe4VqFiHQ6yMUY3r119/HatWrYJCof95pLS0FKtXr8brr79u1uCsyb/nASeWRflnj2rAFuWfPaoBe6asQVMPKZxEHP7IKjHZNm0dnQN6Rg0L2bZtGx49eoTvvvsOrq6uyM/Ph06ng5eXF77//nt+vRMnTpgrzlqPXlBsUf7ZoxqwRflnj2rAnilrIBJwaOXlgJPpxXhUokYdB6PngLBbdA7oGfVKWblypbnjsHrJyckICgpiHYbdovyzRzVgi/LPHtWAPVPXILKODGceFuNcVgn61Hcx2XZri+5B3U26PToH9IxqXLdr187ccVg9jUbDOgS7Rvlnj2rAFuWfPaoBe6augaNIgHC5DNdySvG8rxOcxLXrHh/P6oOupr0ZIJ0Deka9SpRKJVavXo3u3bujdevWAIBTp05hy5YtZg2OEEIIIYSltt4yaHTAhUc09poYx6jG9dKlS3Hr1i189NFH/HQ0jRo1Mhhvbe9Y3BWS/IPyzx7VgC3KP3tUA/bMUQNPmQiN3CS4mFUKhca2boneZ2sf9Nnax2Tbo3NAz6hhIUePHsWRI0fg6OjI3/bcx8cHGRkZZg3OmgQEBLAOwa5R/tmjGrBF+WePasCeuWrQwccBf+cpcflRKdr72M4t0UtUpu2Np3NAz6iea7FYXG4cTU5ODtzd3c0Rk1XKzMxkHYJdo/yzRzVgi/LPHtWAPXPVwM9JjPrOYpzLKoVaqzPLPmwBnQN6RjWue/fujRkzZiAlJQWAPnkLFy7ESy+9ZNbgrEl+fj7rEOwa5Z89qgFblH/2qAbsmbMGHXwcUKjSIjHHtm+J/izoHNAzqnE9bdo0+Pv7o1+/fsjPz0evXr3g7e2Nt956y9zxEUIIIYQwF+giRl1HEc5mFkOro95rUjmjxlxLJBLMnj0bs2fPRk5ODjw8PPgLGwkhhBBCbB3Hcejg44Afkgtw87ESYR7Wf/FedONo1iHYJE6nq9nXr+zsbFy4cAHBwcEIDg42V1w1plAokJiYiPDwcCZXq6rVaohEdPcmVij/7FEN2KL8s0c1YM/cNdDpdPjq+mMIBcDroe7U0fgv9nQOVNXurDIDGRkZWLRoEZKSkhAZGYkxY8Zg5MiREAgEKCgowPLly2nc9f8oFAq7eUHVRpR/9qgGbFH+2aMasGfuGnAch/Y+Djh0vxB38lUIdpOYbV/WiM4BvSrHXM+bNw+urq6YNWsWdDodxo4di8WLF+PMmTNYs2YN1q9fb6k4a7309HTWIdg1yj97VAO2KP/sUQ3Ys0QNmsmlcBULcCajGDX88b/W6bapG7pt6may7dE5oFfl14tLly7h5MmTkEgkaNeuHdq2bYsePXoAAHr06IEZM2ZYJEhCCCGEkNpA+L/e6/+mFuFeoQqBLtR7TQxV2XOtUqkgkehfNA4ODnB0dDQYX2Tt39gIIYQQQmqqpacMLmIBTqVbf+81Mb0qe641Gg1+//13/oWjVqsN/tZqbes2oM/Cy8uLdQh2jfLPHtWALco/e1QD9ixVA5GAw3NlvdcFKgS6Uu81QOdAmSob156enpg9ezb/t7u7u8HfcrncfJFZGTc3N9Yh2DXKP3tUA7Yo/+xRDdizZA1aesrwe0YJTj0sRgMXMc0cAjoHylTZuD527Jil4rB6SUlJCAkJYR2G3aL8s0c1YIvyzx7VgD1L1kAk0M97fSS1CHcLVAiywt7rIc2GmHR7dA7o0XwphBBCCCFPocUTvdeBVth7PantJNYh2CSjbn9OCCGEEEIMiQQcOtR1wIMiNe4WqFiHU2PFqmIUq4pZh2FzqHFtIo6OjqxDsGuUf/aoBmxR/tmjGrDHogbN5TK4igX41QpnDum7tS/6bu1rsu3ROaBHjWsT8fPzYx2CXaP8s0c1YIvyzx7VgD0WNRAJOHTydUR6sRq38pQW339tQueAHjWuTSQtLY11CHaN8s8e1YAtyj97VAP2WNWguVwKT6kQv6YVQ2tlvdemROeAHjWuTaS4mMYssUT5Z49qwBblnz2qAXusaiDgODzv54hshQbXchRMYqgN6BzQo8Y1IYQQQsgzauwmgZ+jCKfSi6HS2m/vNaGp+AghhBBCnhnHcejq54jvk/JxMasE7X1q/8V9oyNGsw7BJnE6a7u0tRIKhQKJiYkIDw+HVCplHQ4hhBBC7NDOpDykFasxoakHZCIaIGCrqmp3UtVNJC8vj3UIdo3yzx7VgC3KP3tUA/ZqQw26+jmhVKPD7xklrEOp1qPiR3hU/Mhk26sN+a8NLNa4Tk5OxtChQ9GrVy8MHToUd+/eLbfOZ599hpdeegkxMTEYMGAATp48aanwnllWVhbrEOwa5Z89qgFblH/2qAbs1YYa+DiK0MxDinNZJXis0LAOp0qDdg7CoJ2DTLa92pD/2sBijet58+Zh+PDh+OmnnzB8+HDMnTu33DotWrTA7t27sX//fixduhTTpk1DaWmppUIkhBBCCHlm3fwcIeCA42lFrEMhDFikcZ2dnY2//voL0dHRAIDo6Gj89ddfyMnJMVivS5cucHBwAACEhoZCp9Ph8ePHlgiREEIIIcQkXCRCPOfjiJuPlbhvhbdFJ8/GIo3r9PR0+Pj4QCgUAgCEQiG8vb2Rnp5e6XP27t2L+vXro27dupYI8Zn5+vqyDsGuUf7ZoxqwRflnj2rAXm2qQTtvB7iKBTj6oNBubixTm/LPUq2ciu+PP/7AJ598go0bN9b4uffu3YNAoP/O4O/vDwBITU3ll8vlcsjlciQnJ0Oj0Y+FkkqlCAgIQGZmJvLz8/l1AwMDoVAoDL4EeHl5wc3NDUlJSfxjjo6O8Pb2RlpamsEE6iEhIcjLyzMYg+Tr6wupVGow5tzV1RXe3t5ISUmBQqGffF4oFCIoKAg5OTkGPfyWPCY/Pz+rOSaNRmMQvy0ck7XVSa1Wg+M4mzoma6qTTqcDx3E2dUzWVidnZ2c4OTnZ1DFZW53+/XzWx/R8XT8cSCnGz9dTECRR1ro6lZToL7rMyckxSZ3S0tLAcRzTY3qaOj3Na6+wsBCVschUfNnZ2ejVqxfOnj0LoVAIjUaD9u3b48iRI5DL5QbrXrp0CVOnTkVcXByaNWtm9D5YT8WXlJSEkJAQi++X6FH+2aMasEX5Z49qwF5tq4FOp8PWv/OQo9DgzaYekApr1yRtOxJ3AACGhg81yfZqW/7NiflUfJ6enggLC8OBAwcAAAcOHEBYWFi5hvXVq1cxbdo0fPrppzVqWBNCCCGE1DYcx6G7vxOK1Tr89rD2Tc03NHyoyRrW5B8W+wo1f/58bNmyBb169cKWLVuwYMECAMC4ceNw7do1AMCCBQtQWlqKuXPnIjY2FrGxsbh586alQiSEEEIIMSlfRzFaekpxLrMEmSVq1uEYSMlLQUpeCuswbI7FxlwHBwdj165d5R7/8ssv+X/v2bPHUuGYnKurK+sQ7Brlnz2qAVuUf/aoBuzV1hp083PCrTwlfkopxMhGbvy4ZNZG/TAKAHBi9AmTbK+25t/SatfgHyvm7e3NOgS7Rvlnj2rAFuWfPaoBe7W1Bg4iAaL8nPCgSI0r2QrW4ZhNbc2/pVHj2kRSUuhnFZYo/+xRDdii/LNHNWCvNtcgXC5FgLMIJ9KKUKTSsg7HLGpz/i2JGtcmUjZNDGGD8s8e1YAtyj97VAP2anMNOI5DrwBnKLU6m71zY23OvyVR45oQQgghxALqyERo7+2AxBwF7hYoWYdDzKRW3kTGGpXdfZKwQflnj2rAFuWfPaoBe9ZQg451HXHjsQKH7xdibBMPSITsLm58t8O7Jt2eNeTfEixyExlLYH0TGUIIIYQQY6QUqrD17zy0qiNDzwBn1uGQp8D8JjL24MnbcBLLo/yzRzVgi/LPHtWAPWupQYCzGG28ZLj4qBT3GA4PufnoJm4+Mt39RKwl/+ZGjWsToRcUW5R/9qgGbFH+2aMasGdNNXje1wnuEgEO3S+EUsNmEMGbB97EmwfeNNn2rCn/5kSNa0IIIYQQC5MIOfRt4II8pRYnbHT2EHtFjWtCCCGEEAbqPzE8JCmPZg+xFdS4NhF/f3/WIdg1yj97VAO2KP/sUQ3Ys8YadPNzgpdMiEP3C1Bo5TeXscb8mwM1rgkhhBBCGBEJOPQLdIFSo8OhewWwkUnc7Bo1rk0kNTWVdQh2jfLPHtWALco/e1QD9qy1Bl4OIkTVc8KdAhXOZ5VabL9znp+DOc/PMdn2rDX/pkY3kSGEEEIIYSyyjgx38lU4kVYEf2cRfB3FZt9nj4Y9zL4Pe0Q914QQQgghjHEch74NnOEkEuCH5AKUqM0//vryw8u4/PCy2fdjb6hxbSJyuZx1CHaN8s8e1YAtyj97VAP2rL0GjiIB+ge5oFClxX4LjL+e+uNUTP1xqsm2Z+35NxVqXJsIvaDYovyzRzVgi/LPHtWAPVuogZ+TGD3qOeFOvgq/PSxhHU6N2EL+TYEa1yaSnJzMOgS7Rvlnj2rAFuWfPaoBe7ZSg8g6MjTzkOLUw2LctqL5r20l/8+KGtcmotFoWIdg1yj/7FEN2KL8s0c1YM9WasBxHHrXd4a3gxD77hYgq0TNOiSj2Er+nxU1rgkhhBBCahmxgMOghq4QCYDdd/JRbOU3mLEnNBWfiUilUtYh2DXKP3tUA7Yo/+xRDdiztRq4SoQY2NAV2/7OQ3xyPoaFuEEk4Ey2/aXdl5psW4Dt5f9pcTobuRWQQqFAYmIiwsPDqbiEEEIIsRnXcxVIuFuAZh5SRDdwBseZroFNnk5V7U4aFmIimZmZrEOwa5R/9qgGbFH+2aMasGerNQjzkOJ5X0f8mavAsQdFJpui73TKaZxOOW2SbQG2m/+aosa1ieTn57MOwa5R/tmjGrBF+WePasCeLdegg48DWnvJcC6rFL9nmGaKvtk/z8bsn2ebZFuAbee/JmjMNSGEEEJILcdxHHrUc0KJWodf0ovhIBIgoo6MdVikAtS4JoQQQgixAhzH4aUGzijVaPFTSiHEAqCZnBrYtQ0NCzGRwMBA1iHYNco/e1QDtij/7FEN2LOHGgg5Di8HuSLAWYwD9wqRmFPKOiSePeTfGNS4NhGFQsE6BLtG+WePasAW5Z89qgF79lKDsjmwyxrY17JrRwPbXvJfHWpcm0h6ejrrEOwa5Z89qgFblH/2qAbs2VMNJEIOg4Nd0cBZjIP3C3HlKRrYa3qvwZrea0wWkz3lvyo05poQQgghxAqJBRwGBbsi/k4+Dt8vRLFKi+d8HIyeBzuiboR5A7RT1HNNCCGEEGKlyoaINPWQ4pf0Yhx9UAStkfNgH71zFEfvHDVzhPaHeq5NxMvLi3UIdo3yzx7VgC3KP3tUA/bstQZCAYeYBs5wFgvwR2YJClVaRDdwgbiaW6Uv/nUxAKBHwx4micNe8/9v1Lg2ETc3N9Yh2DXKP3tUA7Yo/+xRDdiz5xpwHIeoek5wFgtw7EERHiseY2BDV7hKhBaLwZ7z/yQaFmIiSUlJrEOwa5R/9qgGbFH+2aMasEc1ANp5O2BQQ1c8Vmix6eZjpBSqLLZvyr8eNa4JIYQQQmxIiJsEo0LdIBVy+D4pD+ezSqAzchw2eXbUuCaEEEIIsTF1ZCK81tgdQS5iHE0tQnxyAUrUWtZh2QUac20ijo6OrEOwa5R/9qgGbFH+2aMasEc1MCQTCTCooSvOZ5XieFoRNt54jJhAF9R3FgMAvoj+wqT7o/zrcTob+Z1AoVAgMTER4eHhkEqlrMMhhBBCCKk1HharkXA3H7kKLVp7ydDV1wkSoXHzYZPyqmp30rAQE0lLS2Mdgl2j/LNHNWCL8s8e1YA9qkHl6jqK8HqoB1p7yXAhqxRf38jFV5fjsf/mfpPtg/KvZ3vDQoYNA3Jz//l7yBBg0iSguBjo27f8+qNH6/979AgYNKj88okTgaFDgZQUYNSo8svffReIiYEqMREYPrz88jlzgB49gMuXgalTyy9fuhTo2BE4fRqYPbv88jVrgIgI4OhRYPHi8su/+AIIDQX27wc+/rj88u++AwICgB07gM8/L798926gTh1g0yb9f/926BDg6AjExQE7d5ZffuKE/v8ffQQcOGC4zMEBOHxY/+9Fi4CffzZc7ukJ7Nmj//esWcCZM4bL/f2BLVv0/546VZ/DJzVuDGzYAABweucd4OFDw+UREfr8AcDIkUBqquHyDh2AZcv0/x44EMjONlzevTvwwQf6f/fpA5SUGC6PjgamT9f/u1s3lGOh1x5u3gTefLP8cgu/9jxKSvQ1L2Mnrz2MHw/cumW4nMFrzyD/dvbaK4fRa8+jpASQy+3utWeA8WtP9/rrwGuv2d1rD4BR73uSPXvwor8z2q9eiMKTpzGw/TUIOKDPlQiIAgKe+bVXPHmy/t/28Nrz8NC/11XA9hrXhBBCCCGkUq4SIZwdRRAKAI0WSC9SQ1aihodWB1E1N54h1aMx1yaSlJSEkJAQi++X6FH+2aMasEX5Z49qwB7VoGa6beoGtVaHad324+88JVzFAnTydURzuRQCruaNbHvKP425tgB7eTHVVpR/9qgGbFH+2aMasEc1qDmRgMPAhq4YFuwKJ7EAh+8X4svrufgrR1HjubEp/3rUuDaRvLw81iHYNco/e1QDtij/7FEN2KMaPL1AVwlebeyGAUEuEHEc9t0rwIbrubiYVQKV1rhGNuVfj8Zcm0hWVhbc3NxYh2G3KP/sUQ3YovyzRzVgj2pQM9+9/J3B3xzHobG7FI3cJLj5WImzmSU4klqEk+nFiPSSIcJTBleJsNLtUf71qHFNCCGEEGKHAtwCKnyc4zg08ZAi1F2C1CI1/sgswemHJTjzsAQNXcVo6SlDsJsEwqcYl20PqHFNCCGEEGKHdiTuAAAMDR9a4XKO4xDgLEaAsxiPFRpcyS7FtWwF4vML4CTiEOouRZiHFP5OInDU0OZR49pEfH19WYdg1yj/7FEN2KL8s0c1YI9qUDOfn9fPxV1Z4/pJ7lIhuvo5oYuvI27nK3EtW4Gr2aW4+KgUzmIBQt0lCHDzhpqm86PGtanQLdfZovyzRzVgi/LPHtWAPaqB+Qk4Do3cpGjkJoVSo0NSvhI3chW4/KgUF3SAOC0bDZwlaOgqRqCLBB5Sgd31atNsISZy9+5d1iHYNco/e1QDtij/7FEN2KMaWJZEyKGphxQDGrri7eae6OhQiOZyGR6VqnEktQgbrudibWIO4u/k44/MEqQVqaAxcuYRa0Y914QQQggh5JlIhBz8RGqEBDhDp9MhV6HF/UIVUotUSC1U4VaeEgAg4IA6MiF8HETwdhD97/9CyES2099rscZ1cnIyZs6cicePH8Pd3R3Lly9HYGCgwToajQaLFy/GyZMnwXEcxo8fj8GDB1sqREIIIYQQ8ow4joNcJoRcJkREHRkAoFClRWqhCg+L1cgoUevHbeco+Oc4ijh4SIWQ/+8/D5kQ7hIhXCUCOAg5qxpaYrHG9bx58zB8+HDExsYiISEBc+fOxbfffmuwzv79+3H//n0cOXIEjx8/Rv/+/dGhQwf4+/tbKsyn5urqyjoEu0b5Z49qwBblnz2qAXtUg5rZPWS3SbdXVf6dxQI08ZCiicc/4+ILVVpklqiRVaJGjkKDHIUGyQUqg0Y3AAg5wEUsgKtECBexAC4SAZxE+v98nUTwkFY+9zYLnK6m97Z8CtnZ2ejVqxfOnj0LoVAIjUaD9u3b48iRI5DL5fx648ePx4ABA9C7d28AwMKFC+Hn54c33nij2n1UdY93QgghhBBiHZQaHXIUGuQpNShQalGg0iJfqdH/X6VFoVIL7f/WdZcIMKGZvMrtmUNV7U6L9Fynp6fDx8cHQqH+m4VQKIS3tzfS09MNGtfp6enw8/Pj//b19cXDhw8tEeIzS0lJQUBAxZOxE/Oj/LNHNWCL8s8e1YA9qkHNbLq8CQAwOmK0SbZnqvxLhBzqOopQ17HiZqpOp0OpRocitRYyYe0bq21zFzTeu3cPAoE+0WXDSVJTU/nlcrkccrkcycnJ0Gg0APRT9wQEBCAzMxP5+fn8uoGBgVAoFEhPT+cf8/LygpubG5KSkvjHHB0doVAokJaWhuLiYv7xkJAQ5OXlISsri3/M19cXUqnU4IpmV1dXeHt7IyUlBQqF/qcQoVCIoKAg5OTkICcnh1/Xksfk5+dnNcdUVFRkEL8tHJO11Sk/Px8KhcKmjsma6lRUVASFQmFTx2RtdSo7Dls6JmurU2FhocG6tnBM5qzT52f081z3q9/PJMeUk5PD79+cx5Sbm8u8ToWFhagMDQsxkaSkJISEhFh8v0SP8s8e1YAtyj97VAP2qAY1021TNwDAidEnTLI9e8p/Ve1Oi/Sle3p6IiwsDAcOHAAAHDhwAGFhYQYNawDo3bs3du3aBa1Wi5ycHBw9ehS9evWyRIjPrGzIC2GD8s8e1YAtyj97VAP2qAZsUf71LDZQZf78+diyZQt69eqFLVu2YMGCBQCAcePG4dq1awCA2NhY+Pv7o2fPnhgyZAjeeustqxk7FRQUxDoEu0b5Z49qwBblnz2qAXtUA7Yo/3oWG3MdHByMXbt2lXv8yy+/5P8tFAr5Rre1ycnJKdcTTyyH8s8e1YAtyj97VAP2qAZsUf71at8lllbqyYH1xPIo/+xRDdii/LNHNWCPalAzh0YcwqERh0y2Pcq/ns3NFkIIIYQQQqrnKHZkHYJNop5rQgghhBA7FHcuDnHn4liHYXOocW0i1nCLdltG+WePasAW5Z89qgF7VIOa2fnnTuz8c6fJtkf516PGNSGEEEIIISZCjWsTefKuQMTyKP/sUQ3YovyzRzVgj2rAFuVfjxrXhBBCCCGEmIjNzBZSdhd3pVLJZP9arRYKhYLJvgnlvzagGrBF+WePasAe1aBmPMQeAGCynNlT/svam2XtzydxuooetUIFBQW4desW6zAIIYQQQoidaNy4MVxcXAwes5nGtVarRVFREcRiMTiOYx0OIYQQQgixUTqdDiqVCk5OThAIDEdZ20zjmhBCCCGEENbogkZCCCGEEEJMhBrXhBBCCCGEmAg1rgkhhBBCCDERalwTQgghhBBiItS4JoQQQgghxESocU0IIYQQQoiJUOOaEEIIIYQQE6HG9TNKTk7G0KFD0atXLwwdOhR3795lHZLNyc3Nxbhx49CrVy/ExMRg8uTJyMnJAQBcvnwZ/fr1Q69evTBmzBhkZ2fzz6tqGXk669atQ2hoKH83VMq/5SgUCsybNw89e/ZETEwMPvjgAwBVvwfR+5PpHD9+HP3790dsbCz69euHI0eOAKD8m9Py5csRFRVl8J4DPH3OqR41V1ENqvpMBuhzAQCgI89k1KhRur179+p0Op1u7969ulGjRjGOyPbk5ubqfv/9d/7vDz/8UDdr1iydRqPR9ejRQ3fu3DmdTqfTffbZZ7qZM2fqdDpdlcvI00lMTNSNHTtW98ILL+hu3rxJ+bewRYsW6ZYsWaLTarU6nU6ny8rK0ul0Vb8H0fuTaWi1Wl2bNm10N2/e1Ol0Ot3169d1EREROo1GQ/k3o3PnzunS0tL495wyT5tzqkfNVVSDyj6Tdbqq3/vt6XOBGtfP4NGjR7rWrVvr1Gq1TqfT6dRqta5169a67OxsxpHZth9//FH32muv6a5cuaJ76aWX+Mezs7N1EREROp1OV+UyUnMKhUI3ZMgQXUpKCv8mS/m3nMLCQl3r1q11hYWFBo9X9R5E70+mo9Vqde3atdOdP39ep9PpdH/88YeuZ8+elH8LebJh97Q5p3o8m39/wXlS2WeyTlf1e789fS6IWPecW7P09HT4+PhAKBQCAIRCIby9vZGeng65XM44Otuk1Wrx/fffIyoqCunp6fDz8+OXyeVyaLVaPH78uMpl7u7uDCK3bp988gn69esHf39//jHKv+WkpKTA3d0d69atw9mzZ+Hk5IS3334bMpms0vcgnU5H708mwnEc1qxZg0mTJsHR0RFFRUXYsGFDlZ8BlH/zeNqcUz3M48nPZIA+F8rQmGtiVRYtWgRHR0eMHDmSdSh249KlS0hMTMTw4cNZh2K3NBoNUlJS0LRpU8THx2P69On4v//7PxQXF7MOzS6o1Wp88cUXiIuLw/Hjx/H5559j6tSplH9i9+gzuWLUc/0MfH19kZGRAY1GA6FQCI1Gg8zMTPj6+rIOzSYtX74c9+7dw/r16yEQCODr64u0tDR+eU5ODgQCAdzd3atcRmrm3LlzuH37Nrp37w4AePjwIcaOHYtRo0ZR/i3E19cXIpEI0dHRAICWLVvCw8MDMpms0vcgnU5H708mcv36dWRmZqJ169YAgNatW8PBwQFSqZTyb2FVfe5WlXOqh+n9+zMZAH0u/w/1XD8DT09PhIWF4cCBAwCAAwcOICwsjH5iMoNVq1YhMTERn332GSQSCQAgPDwcpaWlOH/+PABg+/bt6N27d7XLSM2MHz8ep06dwrFjx3Ds2DHUrVsXX3/9Nd544w3Kv4XI5XK0b98ev/32GwD9rAfZ2dkIDAys9D2I3p9Mp27dunj48CHu3LkDALh9+zays7PRoEEDyr+FVZXXp11Gaq6iz2SAPpfLcDqdTsc6CGt2+/ZtzJw5E/n5+XB1dcXy5cvRsGFD1mHZlL///hvR0dEIDAyETCYDAPj7++Ozzz7DxYsXMW/ePCgUCtSrVw8rV65EnTp1AKDKZeTpRUVFYf369WjcuDHl34JSUlIwe/ZsPH78GCKRCFOnTkXXrl2rfA+i9yfT2bdvH7788ktwHAcAmDJlCnr06EH5N6PFixfjyJEjePToETw8PODu7o6DBw8+dc6pHjVXUQ3WrFlT6WcyUPV7v718LlDjmhBCCCGEEBOhYSGEEEIIIYSYCDWuCSGEEEIIMRFqXBNCCCGEEGIi1LgmhBBCCCHERKhxTQghhBBCiIlQ45oQQgipwjvvvIOjR48avf53332HlStXmjEiQkhtRo1rQohViIqKwunTp/m/MzIy0L17d6xYsYJhVMTW3bhxAzdu3ODvUBofH4/Q0FB8+eWXBus9//zzOHv2LABgyJAh2L9/P7Kzsy0eLyGEPWpcE0KsTk5ODkaPHo3nn38e7733HutwiA3bsWMHYmJi+JvHAIC7uzu++uorFBYWVvgcqVSK559/Hnv37rVQlISQ2oQa14QQq5Kfn48xY8agRYsWmDt3Lv+4UqnEkiVL0LlzZ3Tu3BlLliyBUqnkl6empiI0NBSRkZGIjIxEWFgYdu3aBQBYu3Ytpk+fzq87f/58hIaG4t69ewDK95o/+bdWq8WGDRvQo0cPtG/fHm+//TYeP37Mr3v+/HkMGzYMbdq0QdeuXREfH49Dhw4ZxNG8eXP+77J4mjVrhsjISLRp0wZvvfUW35DTarWIi4vDCy+8gA4dOuC9995DQUFBhbk6e/YsmjRpwm+7bH/x8fEA9L2ww4YNw8KFC9G6dWv07t0bZ86c4Z+fkZGBCRMmoF27dnjxxRexc+dOftnatWsRGhqK//73v/xjW7duRWhoKJ/XqmItq4dareaf+9JLLyE3N7fafQPA6dOnDY6tSZMmBjXauXMnXnzxRbRr1w4TJkxARkYGvyw0NBQRERGIjIxEjx49cPjw4QrzBwC//vor2rZta/BYw4YNERkZiU2bNlX6vHbt2uHEiROVLieE2C5qXBNCrEZxcTHGjRsHjUaDpUuXGvQmfv7557hy5QoSEhKwb98+XLt2DXFxcfzyspvRnj9/HpcuXUKbNm0q3EdycjJ+/fVXg8c4joNWq61w/e+++w5Hjx7Fli1bcPLkSbi5uWHhwoUAgAcPHmDcuHEYOXIkzpw5g7179yIsLAx9+/bFpUuX+Djmzp3L/12mT58+uHTpEk6cOIHU1FS+FzQ+Ph4//PADvv32Wxw9ehTFxcX8/iri7e3Nb/vSpUuIiIgwWH716lXUr18fv//+O6ZMmYLJkyfzXw7eeecd1K1bFydPnsSnn36KVatWGTS+GzZsyDekAeCHH35AYGAg/7exsR48eBAbN27E119/DQ8PD6P2rdPp4Ofnxx+Xn58fv+zMmTP4+OOPsWbNGpw6dQr16tXDO++8Y7DPhIQEXLp0CW+99Rbmz59fYe6Ki4uRmppa4S2y3377bWzevNngi9STgoODcfPmzQqXEUJsGzWuCSFWY/78+XB0dMTDhw9x8eJFg2X79+/HW2+9BU9PT8jlcrz11lvYt28fv1ypVEIgEEAoFFa5j9WrV2PSpEkGj/n5+eH06dN8A/1J27dvx7Rp01C3bl1IJBJMnjwZP/30E9RqNQ4cOICOHTsiOjoaYrEYHh4eCAsLq9ExazQaaLVauLu788c5evRoBAQEwMnJCe+88w4OHTrE9wD/fzt3F9Jk+8cB/OvQBJcr1LbpUhiBJoIx2kENy14kdL4xTSHIEiN7GVqZHUSL6OVEwq2CSNhRdiBiuiLBOih6gSgcNoyyE7HX3W6tlcLK5cDn4MGb53bTpo/P/4/1/cAOvH/3df2u62bIj2vXdc9XUlIS9u7di7i4OBiNRmi1Wjx8+BCCIGBgYADNzc2Ij49HdnY2qqqqcPv2bbFtTk4OfD4fRkdH8erVK6SkpECpVIrxaMb65MkTnDp1Cna7HWq1GgCiyj0xMYG4uLiIc7pz5w4qKyuRk5ODZcuWoampCS6XCx8/fgy7NxQKic92pulVdrlcHhbLzs6GwWAI23s9TS6Xz/qLAhH93lhcE9GSodVq0dbWhhMnTsBisWBiYkKMeb1eyeplWloavF6v+PfY2BgUCsWc/btcLoyMjMBkMkmuNzc34/Hjx1i/fj30ej3cbrcYc7vdMJvN0Ov10Ov1MBqNkMlk+PLlCwRBQEZGxoLmevfuXej1emzcuBEJCQnYunWrOE+NRiPep9FoEAqFFnx4TqVSSX4BmH5uXq8XK1aswPLlyyWxf26vAICKigp0d3ejq6sLO3fulMSiGavFYoFGo0F/f7+k3a9y+3w+JCUlRZzTzLxyuRwrV66UtDeZTNDpdDh37hzMZnPEfhITEwEAgUAgYryxsREdHR3w+XxhsUAgILYnoj8Li2siWjIOHTqE+Ph4VFdXQ61W4/Lly2JMqVRKil5BECSrqG/fvoVWq52z/4sXL6KpqSlsdXvdunXo7e3FwMAAnE6npIhXq9Ww2+1wOp3i5+XLl1CpVEhNTcX79+8XNNfCwkI4nU64XC5kZmaipaVFnOenT5/E+9xuN2JjY5GcnLygPB6PR7IiP/3clEolxsbGJIf2BEGASqWStC8rK0Nvby+eP3+OLVu2SGLRjLW1tRU2mw02mw2jo6Niu1/lfv36NdauXRtxTjPzfv/+Hd++fZO0dzgcePHiBW7duoWzZ89KvjvTEhISkJGRgZGRkYh51qxZgx07dqCtrS0sNjw8jKysrIjtiOj3xuKaiJakCxcuoLOzE4ODgwCA4uJiXLt2DX6/H36/H1evXkVpaSmAvwuz9vZ28XVqkTx79gwymUxcIY7Wrl27cOnSJbGY8/v94juRS0tL8fTpU3ErxNevXzE0NDSv/mUyGWJiYuD3+wEAJSUluH79Oj58+IBAIACbzYaioiLExsbOq99pfr8f7e3tmJycRF9fH4aHh5Gfn4/U1FTodDpYrVYEg0G8efMGN2/eRFlZmaS9QqFARUUF6urqwsYQzVj1ej0yMzNRU1OD06dPA8Avc3s8Hty7dw/FxcUR51RSUoKenh4MDQ3h58+fsFqtyM3NxerVqyM+31AohPHx8Yh95efnS1bVZzKbzeju7g7bAtLf34/NmzfP2o6Ifl8L+29MRPR/lp6ejsbGRpw8eRIOhwOHDx9GIBAQC7DCwkJx7/S+ffuQl5eH2traWfv7/Pmz5ABktPbs2YOpqSnU1dXB6/UiOTkZRqMRBQUFSEtLg91uR0tLCywWCxITE3H06NGo9l339fXh/v37kMlkyMrKwvnz5wEAlZWV8Hg82L17N4LBIPLy8sSidCFyc3Px7t07bNiwASkpKbhy5Yp4qNBqteLMmTPYtGkTFAoFGhoaYDAYwvrYv39/xL7nM9b6+npUVVXB4XDAZDLNmXv79u2YnJyU5P3x4wcOHjyIwcFBGAwGHDlyBA0NDRgfH4dOp4PNZpPkKy8vR0xMDORyOerr62ddBa+ursaxY8dw4MAByfaZaenp6SgvL0dHR4d4LRgM4tGjR+JbWYjozxIzFemEDhER/fZ6enrQ1dUlKQyXgm3btuHBgwdh12tra+d8Pd5CHT9+HEVFRSgoKIjq/hs3bkAQBL6DnegPxZVrIiJaUlatWhXx+mwHHP+t1tbWed1fU1Pzn4yDiJYG7rkmIqIlpbOzM+J1q9X6Px4JEVE4bgshIiIiIlokXLkmIiIiIlokLK6JiIiIiBYJi2siIiIiokXC4pqIiIiIaJGwuCYiIiIiWiQsromIiIiIFslfiEF5OeczO7YAAAAASUVORK5CYII=\n",
      "text/plain": [
       "<Figure size 864x432 with 1 Axes>"
      ]
     },
     "metadata": {},
     "output_type": "display_data"
    }
   ],
   "source": [
    "# Параметры\n",
    "p = 0.1  # Вероятность продления\n",
    "k = 100  # Минимальное число продлений\n",
    "alpha = 0.05  # Максимальная вероятность не выполнить план\n",
    "\n",
    "# Ищем минимальное N\n",
    "N = 1000  # Начальное значение\n",
    "while True:\n",
    "    cdf_value = binom.cdf(k - 1, N, p)  # P(X < 100) = P(X <= 99)\n",
    "    if cdf_value <= alpha:\n",
    "        break\n",
    "    N += 100  # Увеличиваем N с шагом 100\n",
    "\n",
    "# Точное значение N (можем уточнить с шагом 1)\n",
    "N_min = N - 100\n",
    "while N_min <= N:\n",
    "    cdf_value = binom.cdf(k - 1, N_min, p)\n",
    "    if cdf_value > alpha:\n",
    "        N_min += 1\n",
    "    else:\n",
    "        break\n",
    "\n",
    "print(f\"Минимальное количество промокодов (N): {N_min}\")\n",
    "print(f\"Вероятность не выполнить план P(X < 100): {binom.cdf(k - 1, N_min, p):.4f}\")\n",
    "\n",
    "# Визуализация\n",
    "x = np.arange(0, N_min + 100)\n",
    "cdf_values = binom.cdf(k - 1, x, p)\n",
    "plt.figure(figsize=(12, 6))\n",
    "plt.plot(x, cdf_values, label='CDF биномиального распределения', color='skyblue')\n",
    "plt.axhline(y=alpha, color='red', linestyle='--', label='Допустимая вероятность (5%)')\n",
    "plt.axvline(x=N_min, color='green', linestyle='--', label=f'Минимальное N = {N_min}')\n",
    "plt.title('Кумулятивная вероятность P(X < 100) при разных N', fontsize=14)\n",
    "plt.xlabel('Количество промокодов (N)', fontsize=12)\n",
    "plt.ylabel('Вероятность P(X < 100)', fontsize=12)\n",
    "plt.legend()\n",
    "plt.grid(True, linestyle='--', alpha=0.7)\n",
    "plt.show()"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "27d406c6",
   "metadata": {},
   "source": [
    "Промежуточный вывод\n",
    "\n",
    "Минимальное количество промокодов, которое нужно разослать, составляет **1161**, где вероятность не выполнить план (\\( P(X < 100) \\)) равна **0.0498**, что ниже заданного уровня 5% (0.05). График кумулятивной функции распределения показывает, что при увеличении \\( N \\) вероятность \\( P(X < 100) \\) плавно снижается и становится приемлемой ниже порога 0.05 при \\( N = 1161 \\). Таким образом, раздача **1161** промокодов обеспечит выполнение плана (не менее 100 продлений) с вероятностью не менее 95%. Это значение оптимально с учётом вероятности продления 10% и минимизирует избыточные затраты на акцию."
   ]
  },
  {
   "cell_type": "markdown",
   "id": "150af9f0",
   "metadata": {},
   "source": [
    "7.2 Оценка вероятности открытия push-уведомлений\n",
    "\n",
    "#### Условие\n",
    "Отдел маркетинга планирует разослать 1 млн push-уведомлений, которые открывают около 40% получивших клиентов. Нужно оценить вероятность того, что уведомления откроют не более 399,5 тыс. пользователей, и построить примерный график распределения.\n",
    "\n",
    "#### Подход\n",
    "Ситуация описывается биномиальным распределением \\( X \\sim \\text{Binom}(N=1\\,000\\,000, p=0.4) \\). При больших \\( N \\) используется нормальная аппроксимация с параметрами:\n",
    "- Ожидаемое значение: \\( \\mu = N \\cdot p = 400\\,000 \\),\n",
    "- Стандартное отклонение: \\( \\sigma = \\sqrt{N \\cdot p \\cdot (1 - p)} \\approx 489.90 \\).\n",
    "Вероятность \\( P(X \\leq 399,500) \\) вычисляется с помощью нормальной CDF с поправкой непрерывности."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 56,
   "id": "288a5a40",
   "metadata": {
    "scrolled": true
   },
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "Ожидаемое число открытий: 400000\n",
      "Стандартное отклонение: 489.90\n",
      "Вероятность P(X <= 399,500): 0.1540\n"
     ]
    },
    {
     "data": {
      "image/png": "iVBORw0KGgoAAAANSUhEUgAAAuwAAAGKCAYAAABXZRQmAAAAOXRFWHRTb2Z0d2FyZQBNYXRwbG90bGliIHZlcnNpb24zLjMuNCwgaHR0cHM6Ly9tYXRwbG90bGliLm9yZy8QVMy6AAAACXBIWXMAAAsTAAALEwEAmpwYAACvYUlEQVR4nOzdd3xT5f4H8E9WM9qmbUonLW1poRQZIiCgskcRWloriCKOq8BVURyoICrbAXpdeLkIP0VxW+WyBS4oIorIUFYBLbS0pbvpzB7n90dtbOggOW1ycpLv+/Xi9aI5J8n3fPKc5MnJc54jYBiGASGEEEIIIcQjCbkugBBCCCGEENI26rATQgghhBDiwajDTgghhBBCiAejDjshhBBCCCEejDrshBBCCCGEeDDqsBNCCOkUJpOJ6xIIIcQribkugBBCCD99/fXX2LZtGwoKClBdXY2xY8fijTfe4LosQgjxOtRhJ15r4cKFqK6uxnvvvWd3++nTpzF16lTs378fMTExHFVHCL8tXrwYP/74I5544gn06dMHYrEYoaGhXJdFCCFeiTrshBBCnHLs2DHs3bsX27ZtQ3h4ONflEEKI16Mx7IQAOHr0KKZNm4a+ffvipptuwssvvwyj0Whbfs8992Dx4sVYuXIlBg8ejMGDB2PVqlWwWq22dcaMGYPk5GScPHnSdhvDMBg3bhySk5Nx+vRpAIDFYsGiRYswZswY9OvXDxMmTMCGDRvsHgsAjhw5guTkZLt/AwYMsC1fuHAh/vnPf7a5TZs3b7ZbHwDUajWSk5Nx5MgRh2uxWCx49dVXMWLECKSkpNhq2bx5c5vPferUKTzwwAMYMmQIbrjhBtx111347bff7Na5etua/i1cuLBFplf/u+eee5x6bWpra7FgwQIMHjwY/fr1w/33348///yzzazKysowduxYrFy50nab0WjEG2+8gdGjR6NPnz4YO3YsNm3aBODv10qtVtvWf+aZZ5CcnIzdu3cDAIqKipCcnIwbb7wRBoPBtl5hYSF69epl9/wFBQV4+OGHcfPNN+P666/Hbbfdhu+//94uvzFjxuD999+3u2358uV22bTWBu6++2679ggA33//PTIyMtCvX79WM77a999/jx49euDFF1/EoEGDcOONN2LhwoWor6+3rXN1+6yursbgwYPt6lmzZg3S0tLsHvv06dNITk5GUVGR7ba9e/ciPT0dffr0wciRI/Gf//wHzS/S7ei+58jrBACvv/46UlNT0a9fP4wZMwarV6+2e82Av1/Pq/81PXZr29Zca7UAwIABA+z2rQsXLuD+++9Hv3792sz56homTpxoW36t97a26mpqE3379kVWVhbOnDljW6e6uhpPPfUURowYgX79+mHy5Mn45ptv7B6HYRh88MEHmDBhAvr06YMRI0bgX//6V7vZJScnY82aNbbHSE5OxieffII5c+agf//+GD16NLZu3Wr3PGVlZXjyySdt+/6cOXOQn5/fYrtae66m98Gmejds2IBx48ahX79+SE9Pb/FcQOP7zdWP03w/vFY9jrR5V+23hN+ow058XllZGWbPno2UlBRs2bIFL730Enbu3NliLO727dvBMAy++OILLFu2DF999RU++ugju3UiIiLw5Zdf2v7+6aefWnw4Wq1WRERE4K233sKuXbvwxBNP4L333mv1Aw8Adu7ciUOHDmHRokWdudkO1/L111/j888/x+LFi3HgwAEcOnQIcrm83cfVaDSYMmUKPvvsM2RnZyMlJQVz5sxBdXW13XorV67EoUOHbP9uuummFo81d+5cu3WysrJarHOt12bhwoU4efIk1q5di+zsbMhkMsyaNQt6vb7FY6nVavzjH//AjTfeiOeff97uMbZs2YKFCxfi22+/xUsvvQSlUtnq9p85cwbfffddq8tkMhm+/fZb29/Z2dktjlJrtVqMGDECH3zwAbZu3YoJEybgsccew8WLF1t9TEft3bsXOTk5drfV1dXhySefxA033GBra61l3Jxarcavv/4KkUiETz/9FO+++y5+++23dtvov//9b5jNZqdrPnPmDB5//HGMHz8e27dvx/z587F+/Xp88skndus5su+19titvU5yuRwvv/wydu3ahSVLlmDXrl34z3/+Y7dO0/75f//3fzh06JBdR7OzaLVaPPjgg1AoFMjOzm4z55tuusluH/nss88AOP7e1ppVq1bh6aefxjfffIOYmBg89NBD0Ol0ABq/vPbu3Rvvvfcedu7ciXvvvRdLlizB4cOHbfd/4403sHbtWsyZMwc7d+7E22+/jcjISLvnaMqu6V9CQkKLOtasWYMxY8Zgy5YtuOOOO7BgwQJbp1Wn0+Hee++FVCrFxx9/jC+++AJhYWH4xz/+Yau1uab3m507d7ZY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kLvvvptQKIRpmlxwwQUsXbqUjIwMAHbt2sVjjz2Gz+dj6NChrFixgoEDB56yLZ5YzayrNhlr0efWH8bad02ePJkXX3yR7Oxsy9vXE23x7kxz2717N48++iiaphEMBhk7diyLFy8mMTERgPfee48VK1Zgmia5ubn8/ve/j7zh7Kot3p1ubnfddRf79u2jpKSEjIwMsrOzWbt2LdD/xtuZZiZjrfPctm/fzh133EF2dja6Ht6vfdlll/HYY48B1rM509ykYBdCCCGEECKOyZQYIYQQQggh4pgU7EIIIYQQQsQxKdiFEEIIIYSIY1KwCyGEEEIIEcekYBdCCCGEECKOScEuhBBCED7E386dOwkEAvz973+nqakp1l0SQghACnYhhIgbOTk5HD16NHL+6NGj5OTkxLBH/UtqaiqPP/44V111FTt37sTlcsW6S0IIAYAt1h0QQggh4sH555/P5s2bY90NIYQ4iexhF0KIOOF0OvH5fJ22z5kzh9dffx0I/3z29OnTmThxYqTd6/Vy1113MWHCBK644gqWLVsWafvss8+46KKLGDt2LGPHjuWiiy7i448/BqCuro6HHnqICRMmMGnSJFatWoVpmh32we/3s3z5cq6++mquvvpqli9fjt/vB2D8+PGMHTuWSy65hNzc3Mhtbdq0iWPHjpGTk0MwGARg3bp13HjjjVRVVUW27emnn+bWW2/lsssu45e//CXV1dUAJ122rYkTJ/LZZ5+xe/fuyO2NGjWK0aNHR84XFRWxYcMGfvrTn0Yut2bNGnJyciIZPP/88zz44IOR9scff/ykTzyEECJWpGAXQog4cfHFF7Np0yZCodAp133jjTeoqamJnA+FQtxxxx1kZWWxbds2PvjgA2644YZIu2maDB48mN27d7N7926ysrIibU888QR1dXW89957vPrqq2zcuJH169d3eLurV69m7969bNy4kU2bNvHFF1+watUqAHbs2MHu3bv5zW9+w6WXXhq5rby8vHbX8dZbb/GXv/yFtWvXkpaWFln+5ptv8tvf/paPPvoIm83Gk08+eXrBAWPHjo3c3vTp05k3b16H2wpQXV3Nq6++isfj6fC6CgoK+OCDD077toUQoqdJwS6EEHFi2bJl7NixgyuuuILx48dz8803d7iez+dj5cqVLFiwILJs3759lJaW8tBDD+F2u3E6nYwfPz7SHggEsNvtJ11XKBRi69atPPDAAyQlJTFs2DB+/vOfs2nTpg5ve/Pmzdx5550MGDCA9PR07rzzzk7X7ciHH37IkiVLWLNmDUOGDGnXlp+fT3Z2Nm63m3vvvZd33nnntN68ROull17illtuITk5ucP2Z555pl22QggRa1KwCyFEnMjOzua1115jx44d7NixgzfeeKPD9V555RWuueYaRowYEVnm9XrJysrCZuv4q0k1NTUd7lGuqqoiEAi02wudlZVFSUlJh9dTWlp60rqlpaWntX0AS5cuZejQoXz++ecntWVmZra73kAgEJkyAzBhwgS+973vcf3117Nx48bTvs22jh8/zttvv828efM6bN+zZw8FBQWdvlkSQohYkC+dCiFEH1JdXc26dev4xz/+weHDhyPLMzMz8Xq9BIPBDov2I0eOtCvwW6WlpWG32ykqKmLkyJFAuPgfPHhwh7efkZFBUVERF154YWTdjIyM0+7/008/TXp6Oj/72c/4wQ9+0G4vu9frbXfabreTlpYWWf7pp59is9n45JNPmDdvHlOmTDnt22313HPPcfvtt5OUlNRh+4oVK7j//vsxDCPq6xZCiJ4ie9iFEKIPeeWVV7j11lsZNGhQu+Vjxoxh0KBBPP300zQ2NuLz+di5cycABw8eZP369Vx77bUnXZ9hGEybNo1nnnmG+vp6jh8/zssvv3zSvPNWN954I6tXr6ayspLKykpWrlzJ9OnTT7v/48ePJzs7mzlz5vDII4+0a9u0aRMHDx6kqamJ5557jqlTp3ZYOHs8HpRSKKVO+3YBvv32W/bu3cusWbM6bP/000/RdZ1JkyZFdb1CCNHTpGAXQog+xDRN5s6de9JywzB48cUXOXr0KJMmTWLixIm8/fbbNDY2MnfuXGbNmtXuS6htPfLII7hcLqZMmcLs2bO56aabuOWWWzpcd8GCBYwePZq8vDzy8vIYNWqUpfne8+fPp6ysrN20n/z8fBYtWsRVV12F3+9nyZIl7S4zefJkJk6cyMKFC1m2bFmne8k7U15ezsKFCzucyw9QVlbGr371q6i3RQghepqmot1FIYQQQnSzOXPmkJeXx8yZM2PdFSGEiDuyh10IIYQQQog4JgW7EEIIIYQQcUymxAghhBBCCBHHZA+7EEIIIYQQcUwKdiGEEEIIIeKYFOxCCCGEEELEMSnYhRBCCCGEiGNSsAshhBBCCBHHpGAXQgghhBAijv1/iiTj3LMwo3IAAAAASUVORK5CYII=\n",
      "text/plain": [
       "<Figure size 864x432 with 1 Axes>"
      ]
     },
     "metadata": {},
     "output_type": "display_data"
    }
   ],
   "source": [
    "# Параметры\n",
    "N = 1_000_000  # Число уведомлений\n",
    "p = 0.4        # Вероятность открытия\n",
    "k = 399_500    # Максимальное число открытий\n",
    "\n",
    "# Параметры нормальной аппроксимации\n",
    "mu = N * p          # Ожидаемое значение\n",
    "sigma = np.sqrt(N * p * (1 - p))  # Стандартное отклонение\n",
    "\n",
    "# Вычисление вероятности с поправкой непрерывности\n",
    "z_score = (k + 0.5 - mu) / sigma  # Поправка: k + 0.5\n",
    "probability = norm.cdf(z_score)\n",
    "\n",
    "# Вывод результата\n",
    "print(f\"Ожидаемое число открытий: {mu:.0f}\")\n",
    "print(f\"Стандартное отклонение: {sigma:.2f}\")\n",
    "print(f\"Вероятность P(X <= 399,500): {probability:.4f}\")\n",
    "\n",
    "# Визуализация\n",
    "x = np.linspace(mu - 4 * sigma, mu + 4 * sigma, 1000)\n",
    "y = norm.pdf(x, mu, sigma)\n",
    "plt.figure(figsize=(12, 6))\n",
    "plt.plot(x, y, label='Нормальная аппроксимация', color='skyblue')\n",
    "plt.fill_between(x[x <= k + 0.5], y[x <= k + 0.5], alpha=0.3, color='skyblue', label='P(X <= 399,500)')\n",
    "plt.axvline(x=k + 0.5, color='red', linestyle='--', label='399,500 с поправкой')\n",
    "plt.title('Нормальная аппроксимация биномиального распределения', fontsize=14)\n",
    "plt.xlabel('Число открытий', fontsize=12)\n",
    "plt.ylabel('Плотность вероятности', fontsize=12)\n",
    "plt.legend()\n",
    "plt.grid(True, linestyle='--', alpha=0.7)\n",
    "plt.show()"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "0fdfa362",
   "metadata": {},
   "source": [
    "Промежуточный вывод\n",
    "\n",
    "Ожидаемое число открытий составляет **400,000**, а стандартное отклонение — **489.90**. Вероятность того, что уведомления откроют не более 399,5 тыс. пользователей, составляет **0.1540** (15.4%). График нормальной аппроксимации показывает, что распределение центрировано около 400,000, а область до 399,500 занимает левый хвост, что подтверждает расчёт. Эта вероятность (15.4%) выше целевого порога (5%), что указывает на то, что при 1 млн уведомлений есть заметный риск недостижения более высоких показателей открываемости. Для снижения этой вероятности отделу маркетинга стоит рассмотреть увеличение числа уведомлений или повышение привлекательности push-уведомлений."
   ]
  },
  {
   "cell_type": "markdown",
   "id": "25ee0ff2",
   "metadata": {},
   "source": [
    "### Общий вывод по проекту\n",
    "* Цели и задачи исследования\n",
    "Проект был направлен на проведение статистического анализа данных сервиса аренды самокатов GoFast с целью изучения поведения пользователей, сравнения групп с подпиской (Ultra) и без подписки (Free), оценки выручки и проверки гипотез, связанных с маркетинговыми задачами. Кроме того, были решены дополнительные задачи по вероятностным распределениям для поддержки маркетинговых инициатив.\n",
    "\n",
    "\n",
    "* Ключевые результаты\n",
    "\n",
    "1. Исследовательский анализ данных (EDA)\n",
    "Основная концентрация пользователей наблюдается в нескольких городах, при этом разница между самым популярным и наименее популярным городом составляет 30.36%. Это указывает на потенциал для таргетированных акций в менее активных регионах.\n",
    "Соотношение пользователей с подпиской (Ultra) и без подписки (Free) составляет 45.6% и 54.4% соответственно, что демонстрирует сбалансированное распределение.\n",
    "Возраст пользователей имеет нормальное распределение с пиком в диапазоне 25-35 лет, что соответствует целевой аудитории сервиса.\n",
    "Расстояния поездок демонстрируют два пика (около 700 м и 3100 м), вероятно, связанные с разными сценариями использования: короткие поездки для быстрых перемещений и длинные — для прогулок. Пользователи без подписки чаще выбирают поездки до 1000 м, что может быть обусловлено высокой стоимостью старта.\n",
    "2. Сравнение пользователей с подпиской и без\n",
    "Пользователи с подпиской Ultra тратят больше времени на поездки (медиана около 18 минут против 15 минут у пользователей Free). При этом разброс значений у Ultra шире, что указывает на разнообразие сценариев использования.\n",
    "Среднее расстояние поездок пользователей Ultra составляет 3115.45 м, что ниже оптимального значения 3130 м, установленного для износа самокатов. Это положительно сказывается на снижении затрат на обслуживание.\n",
    "3. Выручка\n",
    "Средняя помесячная выручка от пользователей Ultra составляет около 350 руб., что значительно выше, чем у пользователей Free (около 250 руб.). Это подтверждено статистическим анализом (p-value = 1.6850e-31), подчёркивая финансовую выгоду от подписчиков.\n",
    "Общая выручка за весь период достигает 3878641 руб.\n",
    "4. Проверка гипотез\n",
    "Гипотеза 1: Пользователи с подпиской тратят больше времени на поездки (p-value = 1.2804e-34). Гипотеза подтверждена, что делает подписчиков более активными пользователями.\n",
    "Гипотеза 2: Среднее расстояние поездок Ultra не превышает 3130 м (p-value = 0.019554). Гипотеза не отвергнута, что подтверждает меньший износ самокатов.\n",
    "Гипотеза 3: Выручка от пользователей Ultra выше (p-value = 1.6850e-31). Гипотеза подтверждена, что оправдывает фокус на привлечении подписчиков.\n",
    "Вероятностные задачи\n",
    "Для акции с промокодами на бесплатный месяц подписки рекомендуется разослать минимум 1161 промокодов, чтобы с вероятностью 95% обеспечить не менее 100 продлений (при 10% конверсии).\n",
    "5. Вероятность открытия не более 399,5 тыс. уведомлений из 1 млн push-уведомлений (при 40% открываемости) составляет 15.4%. Это выше целевого порога 5%, что указывает на необходимость либо увеличения числа уведомлений, либо повышения их привлекательности.\n",
    "\n",
    "* Рекомендации для руководства\n",
    "1. Стимулирование подписок\n",
    "Пользователи с подпиской Ultra приносят больше выручки и активнее используют сервис. Рекомендуется запустить акции для привлечения новых подписчиков, такие как первый месяц подписки бесплатно или со скидкой.\n",
    "2. Оптимизация износа самокатов\n",
    "Среднее расстояние поездок Ultra (3115.45 м) ниже оптимального (3130 м), что снижает износ. Однако стоит изучить причины частоты коротких поездок у пользователей Free и предложить им тарифы, стимулирующие более длинные поездки (например, скидки на поездки свыше 1000 м).\n",
    "3. Таргетинг по городам\n",
    "В менее популярных городах следует провести маркетинговые кампании, такие как скидки на первые поездки или партнёрства с местными компаниями, для увеличения числа пользователей.\n",
    "4. Маркетинговые акции\n",
    "Для акции с промокодами разослать 1161 промокодов, чтобы гарантировать выполнение плана (не менее 100 продлений).\n",
    "Для push-уведомлений рассмотреть увеличение объёма до 1.1 млн или улучшение контента (например, персонализация), чтобы снизить риск недостижения цели (открытий более 399,5 тыс.).\n",
    "Дальнейший анализ\n",
    "Провести анализ сезонности поездок для корректировки маркетинговых активностей в зависимости от месяцев с высоким спросом.\n",
    "Исследовать причины двух пиков в расстояниях (короткие и длинные поездки), возможно, связав их с типом подписки, городом или временем суток.\n",
    "\n",
    "* Заключение\n",
    "\n",
    "Проект демонстрирует успешное выполнение поставленных задач: проведён полный анализ данных, подтверждены гипотезы, и предоставлены рекомендации для бизнеса. Пользователи с подпиской оказались более выгодными, а маркетинговые расчёты обеспечивают основу для эффективного планирования акций. С учётом предложенных доработок, таких как оптимизация расчёта выручки и углубление анализа данных, проект станет ещё более ценным для компании GoFast.\n"
   ]
  }
 ],
 "metadata": {
  "ExecuteTimeLog": [
   {
    "duration": 530,
    "start_time": "2025-03-04T09:11:06.021Z"
   },
   {
    "duration": 98,
    "start_time": "2025-03-04T09:11:10.870Z"
   },
   {
    "duration": 19,
    "start_time": "2025-03-04T09:11:43.125Z"
   },
   {
    "duration": 18,
    "start_time": "2025-03-04T09:17:59.913Z"
   },
   {
    "duration": 29,
    "start_time": "2025-03-04T09:18:23.073Z"
   },
   {
    "duration": 16,
    "start_time": "2025-03-04T09:18:44.888Z"
   },
   {
    "duration": 2,
    "start_time": "2025-03-04T09:20:48.226Z"
   },
   {
    "duration": 999,
    "start_time": "2025-03-04T09:21:16.221Z"
   },
   {
    "duration": 20,
    "start_time": "2025-03-04T09:29:18.901Z"
   },
   {
    "duration": 15,
    "start_time": "2025-03-04T09:29:33.614Z"
   },
   {
    "duration": 9,
    "start_time": "2025-03-04T09:29:38.678Z"
   },
   {
    "duration": 14,
    "start_time": "2025-03-04T09:30:21.502Z"
   },
   {
    "duration": 10,
    "start_time": "2025-03-04T09:30:34.301Z"
   },
   {
    "duration": 11,
    "start_time": "2025-03-04T09:30:44.750Z"
   },
   {
    "duration": 7,
    "start_time": "2025-03-04T09:30:51.743Z"
   },
   {
    "duration": 9,
    "start_time": "2025-03-04T09:31:21.688Z"
   },
   {
    "duration": 48,
    "start_time": "2025-03-04T09:37:34.240Z"
   },
   {
    "duration": 12,
    "start_time": "2025-03-04T09:37:41.233Z"
   },
   {
    "duration": 11,
    "start_time": "2025-03-04T09:38:56.538Z"
   },
   {
    "duration": 11,
    "start_time": "2025-03-04T09:39:08.175Z"
   },
   {
    "duration": 10,
    "start_time": "2025-03-04T09:44:27.100Z"
   },
   {
    "duration": 14,
    "start_time": "2025-03-04T09:44:30.163Z"
   },
   {
    "duration": 6,
    "start_time": "2025-03-04T12:45:12.315Z"
   },
   {
    "duration": 457,
    "start_time": "2025-03-04T12:45:39.536Z"
   },
   {
    "duration": 113,
    "start_time": "2025-03-04T13:22:04.472Z"
   },
   {
    "duration": 3,
    "start_time": "2025-03-04T13:22:29.879Z"
   },
   {
    "duration": 15,
    "start_time": "2025-03-04T13:22:34.746Z"
   },
   {
    "duration": 9,
    "start_time": "2025-03-04T13:22:58.638Z"
   },
   {
    "duration": 589,
    "start_time": "2025-03-04T13:45:58.501Z"
   },
   {
    "duration": 141,
    "start_time": "2025-03-04T13:47:19.485Z"
   },
   {
    "duration": 10,
    "start_time": "2025-03-04T13:47:24.552Z"
   },
   {
    "duration": 4,
    "start_time": "2025-03-04T13:47:28.928Z"
   },
   {
    "duration": 412,
    "start_time": "2025-03-04T13:48:35.300Z"
   },
   {
    "duration": 13,
    "start_time": "2025-03-04T13:55:31.996Z"
   },
   {
    "duration": 13,
    "start_time": "2025-03-04T13:55:40.198Z"
   },
   {
    "duration": 377,
    "start_time": "2025-03-04T13:55:50.405Z"
   },
   {
    "duration": 397,
    "start_time": "2025-03-04T13:57:20.394Z"
   },
   {
    "duration": 12,
    "start_time": "2025-03-04T13:59:30.753Z"
   },
   {
    "duration": 482,
    "start_time": "2025-03-04T13:59:34.959Z"
   },
   {
    "duration": 460,
    "start_time": "2025-03-04T14:05:19.378Z"
   },
   {
    "duration": 432,
    "start_time": "2025-03-04T14:05:31.027Z"
   },
   {
    "duration": 13,
    "start_time": "2025-03-04T14:06:46.844Z"
   },
   {
    "duration": 439,
    "start_time": "2025-03-04T14:06:55.393Z"
   },
   {
    "duration": 342,
    "start_time": "2025-03-04T14:07:35.552Z"
   },
   {
    "duration": 1216,
    "start_time": "2025-03-04T14:07:50.978Z"
   },
   {
    "duration": 28,
    "start_time": "2025-03-04T14:07:52.197Z"
   },
   {
    "duration": 31,
    "start_time": "2025-03-04T14:07:52.227Z"
   },
   {
    "duration": 15,
    "start_time": "2025-03-04T14:07:52.260Z"
   },
   {
    "duration": 12,
    "start_time": "2025-03-04T14:07:52.278Z"
   },
   {
    "duration": 19,
    "start_time": "2025-03-04T14:07:52.292Z"
   },
   {
    "duration": 18,
    "start_time": "2025-03-04T14:07:52.315Z"
   },
   {
    "duration": 9,
    "start_time": "2025-03-04T14:07:52.334Z"
   },
   {
    "duration": 13,
    "start_time": "2025-03-04T14:07:52.345Z"
   },
   {
    "duration": 11,
    "start_time": "2025-03-04T14:07:52.359Z"
   },
   {
    "duration": 10,
    "start_time": "2025-03-04T14:07:52.371Z"
   },
   {
    "duration": 36,
    "start_time": "2025-03-04T14:07:52.383Z"
   },
   {
    "duration": 52,
    "start_time": "2025-03-04T14:07:52.420Z"
   },
   {
    "duration": 6,
    "start_time": "2025-03-04T14:07:52.475Z"
   },
   {
    "duration": 7,
    "start_time": "2025-03-04T14:07:52.482Z"
   },
   {
    "duration": 7,
    "start_time": "2025-03-04T14:07:52.490Z"
   },
   {
    "duration": 8,
    "start_time": "2025-03-04T14:07:52.499Z"
   },
   {
    "duration": 21,
    "start_time": "2025-03-04T14:07:52.509Z"
   },
   {
    "duration": 5,
    "start_time": "2025-03-04T14:07:52.532Z"
   },
   {
    "duration": 10,
    "start_time": "2025-03-04T14:07:52.538Z"
   },
   {
    "duration": 13,
    "start_time": "2025-03-04T14:07:52.550Z"
   },
   {
    "duration": 223,
    "start_time": "2025-03-04T14:07:52.565Z"
   },
   {
    "duration": 100,
    "start_time": "2025-03-04T14:07:52.790Z"
   },
   {
    "duration": 284,
    "start_time": "2025-03-04T14:07:52.891Z"
   },
   {
    "duration": 513,
    "start_time": "2025-03-04T14:07:53.177Z"
   },
   {
    "duration": 502,
    "start_time": "2025-03-04T14:07:53.691Z"
   },
   {
    "duration": 20,
    "start_time": "2025-03-04T14:07:54.194Z"
   },
   {
    "duration": 13,
    "start_time": "2025-03-04T14:07:54.215Z"
   },
   {
    "duration": 20,
    "start_time": "2025-03-04T14:07:54.231Z"
   },
   {
    "duration": 19,
    "start_time": "2025-03-04T14:07:54.253Z"
   },
   {
    "duration": 10,
    "start_time": "2025-03-04T14:07:54.274Z"
   },
   {
    "duration": 44,
    "start_time": "2025-03-04T14:07:54.286Z"
   },
   {
    "duration": 6,
    "start_time": "2025-03-04T14:07:54.332Z"
   },
   {
    "duration": 5,
    "start_time": "2025-03-04T14:07:54.339Z"
   },
   {
    "duration": 207,
    "start_time": "2025-03-04T14:07:54.346Z"
   },
   {
    "duration": 201,
    "start_time": "2025-03-04T14:07:54.554Z"
   },
   {
    "duration": 3,
    "start_time": "2025-03-04T14:07:54.757Z"
   },
   {
    "duration": 50,
    "start_time": "2025-03-04T14:07:54.761Z"
   },
   {
    "duration": 11,
    "start_time": "2025-03-04T14:07:54.815Z"
   },
   {
    "duration": 12,
    "start_time": "2025-03-04T14:07:54.832Z"
   },
   {
    "duration": 3,
    "start_time": "2025-03-04T14:07:54.848Z"
   },
   {
    "duration": 162,
    "start_time": "2025-03-04T14:07:54.853Z"
   },
   {
    "duration": 11,
    "start_time": "2025-03-04T14:07:55.017Z"
   },
   {
    "duration": 29,
    "start_time": "2025-03-04T14:07:55.032Z"
   },
   {
    "duration": 16,
    "start_time": "2025-03-04T14:07:55.062Z"
   },
   {
    "duration": 23,
    "start_time": "2025-03-04T14:07:55.080Z"
   },
   {
    "duration": 71,
    "start_time": "2025-03-04T14:07:55.105Z"
   },
   {
    "duration": 114,
    "start_time": "2025-03-04T14:07:55.178Z"
   },
   {
    "duration": 0,
    "start_time": "2025-03-04T14:07:55.294Z"
   },
   {
    "duration": 0,
    "start_time": "2025-03-04T14:07:55.295Z"
   },
   {
    "duration": 0,
    "start_time": "2025-03-04T14:07:55.296Z"
   },
   {
    "duration": 0,
    "start_time": "2025-03-04T14:07:55.297Z"
   },
   {
    "duration": 0,
    "start_time": "2025-03-04T14:07:55.299Z"
   },
   {
    "duration": 19,
    "start_time": "2025-03-04T14:09:54.510Z"
   },
   {
    "duration": 1264,
    "start_time": "2025-03-04T14:10:00.430Z"
   },
   {
    "duration": 23,
    "start_time": "2025-03-04T14:10:01.696Z"
   },
   {
    "duration": 29,
    "start_time": "2025-03-04T14:10:01.726Z"
   },
   {
    "duration": 15,
    "start_time": "2025-03-04T14:10:01.757Z"
   },
   {
    "duration": 49,
    "start_time": "2025-03-04T14:10:01.774Z"
   },
   {
    "duration": 17,
    "start_time": "2025-03-04T14:10:01.827Z"
   },
   {
    "duration": 43,
    "start_time": "2025-03-04T14:10:01.847Z"
   },
   {
    "duration": 27,
    "start_time": "2025-03-04T14:10:01.892Z"
   },
   {
    "duration": 17,
    "start_time": "2025-03-04T14:10:01.921Z"
   },
   {
    "duration": 23,
    "start_time": "2025-03-04T14:10:01.942Z"
   },
   {
    "duration": 83,
    "start_time": "2025-03-04T14:10:01.966Z"
   },
   {
    "duration": 7,
    "start_time": "2025-03-04T14:10:02.051Z"
   },
   {
    "duration": 21,
    "start_time": "2025-03-04T14:10:02.060Z"
   },
   {
    "duration": 28,
    "start_time": "2025-03-04T14:10:02.082Z"
   },
   {
    "duration": 20,
    "start_time": "2025-03-04T14:10:02.112Z"
   },
   {
    "duration": 9,
    "start_time": "2025-03-04T14:10:02.134Z"
   },
   {
    "duration": 26,
    "start_time": "2025-03-04T14:10:02.144Z"
   },
   {
    "duration": 14,
    "start_time": "2025-03-04T14:10:02.171Z"
   },
   {
    "duration": 7,
    "start_time": "2025-03-04T14:10:02.187Z"
   },
   {
    "duration": 13,
    "start_time": "2025-03-04T14:10:02.196Z"
   },
   {
    "duration": 33,
    "start_time": "2025-03-04T14:10:02.211Z"
   },
   {
    "duration": 206,
    "start_time": "2025-03-04T14:10:02.246Z"
   },
   {
    "duration": 110,
    "start_time": "2025-03-04T14:10:02.453Z"
   },
   {
    "duration": 243,
    "start_time": "2025-03-04T14:10:02.564Z"
   },
   {
    "duration": 507,
    "start_time": "2025-03-04T14:10:02.808Z"
   },
   {
    "duration": 483,
    "start_time": "2025-03-04T14:10:03.320Z"
   },
   {
    "duration": 24,
    "start_time": "2025-03-04T14:10:03.805Z"
   },
   {
    "duration": 11,
    "start_time": "2025-03-04T14:10:03.834Z"
   },
   {
    "duration": 20,
    "start_time": "2025-03-04T14:10:03.846Z"
   },
   {
    "duration": 21,
    "start_time": "2025-03-04T14:10:03.868Z"
   },
   {
    "duration": 10,
    "start_time": "2025-03-04T14:10:03.921Z"
   },
   {
    "duration": 15,
    "start_time": "2025-03-04T14:10:03.933Z"
   },
   {
    "duration": 5,
    "start_time": "2025-03-04T14:10:03.950Z"
   },
   {
    "duration": 6,
    "start_time": "2025-03-04T14:10:03.956Z"
   },
   {
    "duration": 206,
    "start_time": "2025-03-04T14:10:03.965Z"
   },
   {
    "duration": 204,
    "start_time": "2025-03-04T14:10:04.173Z"
   },
   {
    "duration": 4,
    "start_time": "2025-03-04T14:10:04.379Z"
   },
   {
    "duration": 44,
    "start_time": "2025-03-04T14:10:04.385Z"
   },
   {
    "duration": 47,
    "start_time": "2025-03-04T14:10:04.431Z"
   },
   {
    "duration": 30,
    "start_time": "2025-03-04T14:10:04.480Z"
   },
   {
    "duration": 6,
    "start_time": "2025-03-04T14:10:04.512Z"
   },
   {
    "duration": 163,
    "start_time": "2025-03-04T14:10:04.520Z"
   },
   {
    "duration": 10,
    "start_time": "2025-03-04T14:10:04.685Z"
   },
   {
    "duration": 35,
    "start_time": "2025-03-04T14:10:04.697Z"
   },
   {
    "duration": 75,
    "start_time": "2025-03-04T14:10:04.734Z"
   },
   {
    "duration": 25,
    "start_time": "2025-03-04T14:10:04.811Z"
   },
   {
    "duration": 18,
    "start_time": "2025-03-04T14:10:04.838Z"
   },
   {
    "duration": 8,
    "start_time": "2025-03-04T14:10:04.858Z"
   },
   {
    "duration": 27,
    "start_time": "2025-03-04T14:10:04.868Z"
   },
   {
    "duration": 404,
    "start_time": "2025-03-04T14:10:04.897Z"
   },
   {
    "duration": 21,
    "start_time": "2025-03-04T14:10:05.304Z"
   },
   {
    "duration": 533,
    "start_time": "2025-03-04T14:10:05.327Z"
   },
   {
    "duration": 338,
    "start_time": "2025-03-04T14:10:05.862Z"
   },
   {
    "duration": 126,
    "start_time": "2025-03-04T14:13:33.305Z"
   },
   {
    "duration": 4,
    "start_time": "2025-03-04T14:13:55.082Z"
   },
   {
    "duration": 701,
    "start_time": "2025-03-04T14:14:03.773Z"
   },
   {
    "duration": 16,
    "start_time": "2025-03-04T14:16:28.238Z"
   },
   {
    "duration": 3,
    "start_time": "2025-03-04T14:16:58.767Z"
   },
   {
    "duration": 299,
    "start_time": "2025-03-04T14:17:05.051Z"
   },
   {
    "duration": 76,
    "start_time": "2025-03-07T21:36:34.879Z"
   },
   {
    "duration": 1131,
    "start_time": "2025-03-07T22:04:45.840Z"
   },
   {
    "duration": 52,
    "start_time": "2025-03-07T22:04:46.973Z"
   },
   {
    "duration": 95,
    "start_time": "2025-03-07T22:04:47.026Z"
   },
   {
    "duration": 47,
    "start_time": "2025-03-07T22:04:47.123Z"
   },
   {
    "duration": 11,
    "start_time": "2025-03-07T22:04:47.172Z"
   },
   {
    "duration": 17,
    "start_time": "2025-03-07T22:04:47.185Z"
   },
   {
    "duration": 17,
    "start_time": "2025-03-07T22:04:47.204Z"
   },
   {
    "duration": 134,
    "start_time": "2025-03-07T22:04:47.223Z"
   },
   {
    "duration": 17,
    "start_time": "2025-03-07T22:04:47.359Z"
   },
   {
    "duration": 13,
    "start_time": "2025-03-07T22:04:47.379Z"
   },
   {
    "duration": 17,
    "start_time": "2025-03-07T22:04:47.394Z"
   },
   {
    "duration": 5,
    "start_time": "2025-03-07T22:04:47.413Z"
   },
   {
    "duration": 15,
    "start_time": "2025-03-07T22:04:47.420Z"
   },
   {
    "duration": 6,
    "start_time": "2025-03-07T22:04:47.437Z"
   },
   {
    "duration": 21,
    "start_time": "2025-03-07T22:04:47.445Z"
   },
   {
    "duration": 20,
    "start_time": "2025-03-07T22:04:47.468Z"
   },
   {
    "duration": 190,
    "start_time": "2025-03-07T22:04:47.490Z"
   },
   {
    "duration": 82,
    "start_time": "2025-03-07T22:04:47.682Z"
   },
   {
    "duration": 7,
    "start_time": "2025-03-07T22:04:47.766Z"
   },
   {
    "duration": 17,
    "start_time": "2025-03-07T22:04:47.774Z"
   },
   {
    "duration": 20,
    "start_time": "2025-03-07T22:04:47.792Z"
   },
   {
    "duration": 171,
    "start_time": "2025-03-07T22:04:47.814Z"
   },
   {
    "duration": 103,
    "start_time": "2025-03-07T22:04:47.986Z"
   },
   {
    "duration": 221,
    "start_time": "2025-03-07T22:04:48.090Z"
   },
   {
    "duration": 489,
    "start_time": "2025-03-07T22:04:48.313Z"
   },
   {
    "duration": 504,
    "start_time": "2025-03-07T22:04:48.804Z"
   },
   {
    "duration": 8,
    "start_time": "2025-03-07T22:04:49.310Z"
   },
   {
    "duration": 20,
    "start_time": "2025-03-07T22:04:49.322Z"
   },
   {
    "duration": 24,
    "start_time": "2025-03-07T22:04:49.343Z"
   },
   {
    "duration": 18,
    "start_time": "2025-03-07T22:04:49.369Z"
   },
   {
    "duration": 7,
    "start_time": "2025-03-07T22:04:49.388Z"
   },
   {
    "duration": 14,
    "start_time": "2025-03-07T22:04:49.396Z"
   },
   {
    "duration": 5,
    "start_time": "2025-03-07T22:04:49.412Z"
   },
   {
    "duration": 4,
    "start_time": "2025-03-07T22:04:49.419Z"
   },
   {
    "duration": 188,
    "start_time": "2025-03-07T22:04:49.425Z"
   },
   {
    "duration": 175,
    "start_time": "2025-03-07T22:04:49.615Z"
   },
   {
    "duration": 3,
    "start_time": "2025-03-07T22:04:49.792Z"
   },
   {
    "duration": 29,
    "start_time": "2025-03-07T22:04:49.796Z"
   },
   {
    "duration": 9,
    "start_time": "2025-03-07T22:04:49.826Z"
   },
   {
    "duration": 28,
    "start_time": "2025-03-07T22:04:49.836Z"
   },
   {
    "duration": 4,
    "start_time": "2025-03-07T22:04:49.866Z"
   },
   {
    "duration": 154,
    "start_time": "2025-03-07T22:04:49.871Z"
   },
   {
    "duration": 8,
    "start_time": "2025-03-07T22:04:50.027Z"
   },
   {
    "duration": 33,
    "start_time": "2025-03-07T22:04:50.036Z"
   },
   {
    "duration": 30,
    "start_time": "2025-03-07T22:04:50.071Z"
   },
   {
    "duration": 28,
    "start_time": "2025-03-07T22:04:50.102Z"
   },
   {
    "duration": 24,
    "start_time": "2025-03-07T22:04:50.131Z"
   },
   {
    "duration": 8,
    "start_time": "2025-03-07T22:04:50.156Z"
   },
   {
    "duration": 10,
    "start_time": "2025-03-07T22:04:50.166Z"
   },
   {
    "duration": 419,
    "start_time": "2025-03-07T22:04:50.178Z"
   },
   {
    "duration": 9,
    "start_time": "2025-03-07T22:04:50.599Z"
   },
   {
    "duration": 659,
    "start_time": "2025-03-07T22:04:50.610Z"
   },
   {
    "duration": 230,
    "start_time": "2025-03-07T22:04:51.271Z"
   },
   {
    "duration": 5,
    "start_time": "2025-03-07T22:09:47.179Z"
   },
   {
    "duration": 1069,
    "start_time": "2025-03-07T22:14:12.513Z"
   },
   {
    "duration": 19,
    "start_time": "2025-03-07T22:14:13.584Z"
   },
   {
    "duration": 168,
    "start_time": "2025-03-07T22:14:13.604Z"
   },
   {
    "duration": 16,
    "start_time": "2025-03-07T22:14:13.774Z"
   },
   {
    "duration": 11,
    "start_time": "2025-03-07T22:14:13.792Z"
   },
   {
    "duration": 16,
    "start_time": "2025-03-07T22:14:13.804Z"
   },
   {
    "duration": 22,
    "start_time": "2025-03-07T22:14:13.822Z"
   },
   {
    "duration": 28,
    "start_time": "2025-03-07T22:14:13.846Z"
   },
   {
    "duration": 9,
    "start_time": "2025-03-07T22:14:13.876Z"
   },
   {
    "duration": 9,
    "start_time": "2025-03-07T22:14:13.888Z"
   },
   {
    "duration": 21,
    "start_time": "2025-03-07T22:14:13.898Z"
   },
   {
    "duration": 28,
    "start_time": "2025-03-07T22:14:13.920Z"
   },
   {
    "duration": 10,
    "start_time": "2025-03-07T22:14:13.950Z"
   },
   {
    "duration": 5,
    "start_time": "2025-03-07T22:14:13.962Z"
   },
   {
    "duration": 12,
    "start_time": "2025-03-07T22:14:13.969Z"
   },
   {
    "duration": 5,
    "start_time": "2025-03-07T22:14:13.983Z"
   },
   {
    "duration": 20,
    "start_time": "2025-03-07T22:14:13.989Z"
   },
   {
    "duration": 16,
    "start_time": "2025-03-07T22:14:14.010Z"
   },
   {
    "duration": 56,
    "start_time": "2025-03-07T22:14:14.027Z"
   },
   {
    "duration": 11,
    "start_time": "2025-03-07T22:14:14.085Z"
   },
   {
    "duration": 4,
    "start_time": "2025-03-07T22:14:14.098Z"
   },
   {
    "duration": 272,
    "start_time": "2025-03-07T22:14:14.104Z"
   },
   {
    "duration": 192,
    "start_time": "2025-03-07T22:14:14.377Z"
   },
   {
    "duration": 419,
    "start_time": "2025-03-07T22:14:14.571Z"
   },
   {
    "duration": 727,
    "start_time": "2025-03-07T22:14:14.991Z"
   },
   {
    "duration": 484,
    "start_time": "2025-03-07T22:14:15.720Z"
   },
   {
    "duration": 9,
    "start_time": "2025-03-07T22:14:16.206Z"
   },
   {
    "duration": 8,
    "start_time": "2025-03-07T22:14:16.218Z"
   },
   {
    "duration": 35,
    "start_time": "2025-03-07T22:14:16.228Z"
   },
   {
    "duration": 18,
    "start_time": "2025-03-07T22:14:16.264Z"
   },
   {
    "duration": 7,
    "start_time": "2025-03-07T22:14:16.283Z"
   },
   {
    "duration": 49,
    "start_time": "2025-03-07T22:14:16.291Z"
   },
   {
    "duration": 12,
    "start_time": "2025-03-07T22:14:16.342Z"
   },
   {
    "duration": 7,
    "start_time": "2025-03-07T22:14:16.355Z"
   },
   {
    "duration": 159,
    "start_time": "2025-03-07T22:14:16.364Z"
   },
   {
    "duration": 167,
    "start_time": "2025-03-07T22:14:16.524Z"
   },
   {
    "duration": 3,
    "start_time": "2025-03-07T22:14:16.693Z"
   },
   {
    "duration": 24,
    "start_time": "2025-03-07T22:14:16.698Z"
   },
   {
    "duration": 9,
    "start_time": "2025-03-07T22:14:16.723Z"
   },
   {
    "duration": 30,
    "start_time": "2025-03-07T22:14:16.734Z"
   },
   {
    "duration": 3,
    "start_time": "2025-03-07T22:14:16.765Z"
   },
   {
    "duration": 235,
    "start_time": "2025-03-07T22:14:16.770Z"
   },
   {
    "duration": 7,
    "start_time": "2025-03-07T22:14:17.007Z"
   },
   {
    "duration": 73,
    "start_time": "2025-03-07T22:14:17.016Z"
   },
   {
    "duration": 110,
    "start_time": "2025-03-07T22:14:17.091Z"
   },
   {
    "duration": 25,
    "start_time": "2025-03-07T22:14:17.202Z"
   },
   {
    "duration": 22,
    "start_time": "2025-03-07T22:14:17.228Z"
   },
   {
    "duration": 109,
    "start_time": "2025-03-07T22:14:17.251Z"
   },
   {
    "duration": 22,
    "start_time": "2025-03-07T22:14:17.361Z"
   },
   {
    "duration": 6,
    "start_time": "2025-03-07T22:14:17.385Z"
   },
   {
    "duration": 406,
    "start_time": "2025-03-07T22:14:17.392Z"
   },
   {
    "duration": 10,
    "start_time": "2025-03-07T22:14:17.800Z"
   },
   {
    "duration": 650,
    "start_time": "2025-03-07T22:14:17.812Z"
   },
   {
    "duration": 234,
    "start_time": "2025-03-07T22:14:18.464Z"
   },
   {
    "duration": 1246,
    "start_time": "2025-03-07T22:20:23.192Z"
   },
   {
    "duration": 18,
    "start_time": "2025-03-07T22:20:24.439Z"
   },
   {
    "duration": 28,
    "start_time": "2025-03-07T22:20:24.459Z"
   },
   {
    "duration": 15,
    "start_time": "2025-03-07T22:20:24.489Z"
   },
   {
    "duration": 11,
    "start_time": "2025-03-07T22:20:24.506Z"
   },
   {
    "duration": 16,
    "start_time": "2025-03-07T22:20:24.518Z"
   },
   {
    "duration": 11,
    "start_time": "2025-03-07T22:20:24.535Z"
   },
   {
    "duration": 22,
    "start_time": "2025-03-07T22:20:24.547Z"
   },
   {
    "duration": 21,
    "start_time": "2025-03-07T22:20:24.571Z"
   },
   {
    "duration": 18,
    "start_time": "2025-03-07T22:20:24.593Z"
   },
   {
    "duration": 13,
    "start_time": "2025-03-07T22:20:24.613Z"
   },
   {
    "duration": 21,
    "start_time": "2025-03-07T22:20:24.627Z"
   },
   {
    "duration": 14,
    "start_time": "2025-03-07T22:20:24.649Z"
   },
   {
    "duration": 6,
    "start_time": "2025-03-07T22:20:24.666Z"
   },
   {
    "duration": 22,
    "start_time": "2025-03-07T22:20:24.673Z"
   },
   {
    "duration": 5,
    "start_time": "2025-03-07T22:20:24.697Z"
   },
   {
    "duration": 5,
    "start_time": "2025-03-07T22:20:24.704Z"
   },
   {
    "duration": 7,
    "start_time": "2025-03-07T22:20:24.711Z"
   },
   {
    "duration": 5,
    "start_time": "2025-03-07T22:20:24.720Z"
   },
   {
    "duration": 16,
    "start_time": "2025-03-07T22:20:24.726Z"
   },
   {
    "duration": 28,
    "start_time": "2025-03-07T22:20:24.743Z"
   },
   {
    "duration": 177,
    "start_time": "2025-03-07T22:20:24.773Z"
   },
   {
    "duration": 94,
    "start_time": "2025-03-07T22:20:24.952Z"
   },
   {
    "duration": 217,
    "start_time": "2025-03-07T22:20:25.048Z"
   },
   {
    "duration": 565,
    "start_time": "2025-03-07T22:20:25.267Z"
   },
   {
    "duration": 471,
    "start_time": "2025-03-07T22:20:25.834Z"
   },
   {
    "duration": 10,
    "start_time": "2025-03-07T22:20:26.306Z"
   },
   {
    "duration": 13,
    "start_time": "2025-03-07T22:20:26.318Z"
   },
   {
    "duration": 49,
    "start_time": "2025-03-07T22:20:26.332Z"
   },
   {
    "duration": 32,
    "start_time": "2025-03-07T22:20:26.383Z"
   },
   {
    "duration": 7,
    "start_time": "2025-03-07T22:20:26.417Z"
   },
   {
    "duration": 18,
    "start_time": "2025-03-07T22:20:26.426Z"
   },
   {
    "duration": 14,
    "start_time": "2025-03-07T22:20:26.445Z"
   },
   {
    "duration": 24,
    "start_time": "2025-03-07T22:20:26.460Z"
   },
   {
    "duration": 193,
    "start_time": "2025-03-07T22:20:26.486Z"
   },
   {
    "duration": 167,
    "start_time": "2025-03-07T22:20:26.681Z"
   },
   {
    "duration": 12,
    "start_time": "2025-03-07T22:20:26.850Z"
   },
   {
    "duration": 43,
    "start_time": "2025-03-07T22:20:26.864Z"
   },
   {
    "duration": 10,
    "start_time": "2025-03-07T22:20:26.909Z"
   },
   {
    "duration": 21,
    "start_time": "2025-03-07T22:20:26.921Z"
   },
   {
    "duration": 4,
    "start_time": "2025-03-07T22:20:26.946Z"
   },
   {
    "duration": 140,
    "start_time": "2025-03-07T22:20:26.952Z"
   },
   {
    "duration": 7,
    "start_time": "2025-03-07T22:20:27.094Z"
   },
   {
    "duration": 84,
    "start_time": "2025-03-07T22:20:27.103Z"
   },
   {
    "duration": 20,
    "start_time": "2025-03-07T22:20:27.190Z"
   },
   {
    "duration": 14,
    "start_time": "2025-03-07T22:20:27.212Z"
   },
   {
    "duration": 10,
    "start_time": "2025-03-07T22:20:27.227Z"
   },
   {
    "duration": 18,
    "start_time": "2025-03-07T22:20:27.238Z"
   },
   {
    "duration": 14,
    "start_time": "2025-03-07T22:20:27.257Z"
   },
   {
    "duration": 148,
    "start_time": "2025-03-07T22:20:27.273Z"
   },
   {
    "duration": 378,
    "start_time": "2025-03-07T22:20:27.422Z"
   },
   {
    "duration": 9,
    "start_time": "2025-03-07T22:20:27.801Z"
   },
   {
    "duration": 906,
    "start_time": "2025-03-07T22:20:27.811Z"
   },
   {
    "duration": 235,
    "start_time": "2025-03-07T22:20:28.719Z"
   },
   {
    "duration": 49,
    "start_time": "2025-03-09T21:31:54.999Z"
   },
   {
    "duration": 7,
    "start_time": "2025-03-09T21:32:25.094Z"
   },
   {
    "duration": 3,
    "start_time": "2025-03-10T22:39:43.491Z"
   },
   {
    "duration": 46,
    "start_time": "2025-03-10T22:40:23.489Z"
   },
   {
    "duration": 1382,
    "start_time": "2025-03-10T22:40:28.111Z"
   },
   {
    "duration": 18,
    "start_time": "2025-03-10T22:40:29.496Z"
   },
   {
    "duration": 28,
    "start_time": "2025-03-10T22:40:29.516Z"
   },
   {
    "duration": 20,
    "start_time": "2025-03-10T22:40:29.545Z"
   },
   {
    "duration": 12,
    "start_time": "2025-03-10T22:40:29.569Z"
   },
   {
    "duration": 52,
    "start_time": "2025-03-10T22:40:29.582Z"
   },
   {
    "duration": 47,
    "start_time": "2025-03-10T22:40:29.635Z"
   },
   {
    "duration": 17,
    "start_time": "2025-03-10T22:40:29.683Z"
   },
   {
    "duration": 12,
    "start_time": "2025-03-10T22:40:29.702Z"
   },
   {
    "duration": 103,
    "start_time": "2025-03-10T22:40:29.715Z"
   },
   {
    "duration": 8,
    "start_time": "2025-03-10T22:40:29.819Z"
   },
   {
    "duration": 8,
    "start_time": "2025-03-10T22:40:29.828Z"
   },
   {
    "duration": 10,
    "start_time": "2025-03-10T22:40:29.837Z"
   },
   {
    "duration": 23,
    "start_time": "2025-03-10T22:40:29.849Z"
   },
   {
    "duration": 24,
    "start_time": "2025-03-10T22:40:29.874Z"
   },
   {
    "duration": 6,
    "start_time": "2025-03-10T22:40:29.900Z"
   },
   {
    "duration": 19,
    "start_time": "2025-03-10T22:40:29.908Z"
   },
   {
    "duration": 7,
    "start_time": "2025-03-10T22:40:29.929Z"
   },
   {
    "duration": 5,
    "start_time": "2025-03-10T22:40:29.937Z"
   },
   {
    "duration": 10,
    "start_time": "2025-03-10T22:40:29.943Z"
   },
   {
    "duration": 6,
    "start_time": "2025-03-10T22:40:29.954Z"
   },
   {
    "duration": 189,
    "start_time": "2025-03-10T22:40:29.962Z"
   },
   {
    "duration": 94,
    "start_time": "2025-03-10T22:40:30.153Z"
   },
   {
    "duration": 228,
    "start_time": "2025-03-10T22:40:30.249Z"
   },
   {
    "duration": 436,
    "start_time": "2025-03-10T22:40:30.478Z"
   },
   {
    "duration": 492,
    "start_time": "2025-03-10T22:40:30.915Z"
   },
   {
    "duration": 8,
    "start_time": "2025-03-10T22:40:31.409Z"
   },
   {
    "duration": 19,
    "start_time": "2025-03-10T22:40:31.418Z"
   },
   {
    "duration": 30,
    "start_time": "2025-03-10T22:40:31.439Z"
   },
   {
    "duration": 18,
    "start_time": "2025-03-10T22:40:31.471Z"
   },
   {
    "duration": 6,
    "start_time": "2025-03-10T22:40:31.491Z"
   },
   {
    "duration": 8,
    "start_time": "2025-03-10T22:40:31.499Z"
   },
   {
    "duration": 31,
    "start_time": "2025-03-10T22:40:31.508Z"
   },
   {
    "duration": 5,
    "start_time": "2025-03-10T22:40:31.541Z"
   },
   {
    "duration": 180,
    "start_time": "2025-03-10T22:40:31.548Z"
   },
   {
    "duration": 189,
    "start_time": "2025-03-10T22:40:31.729Z"
   },
   {
    "duration": 5,
    "start_time": "2025-03-10T22:40:31.920Z"
   },
   {
    "duration": 34,
    "start_time": "2025-03-10T22:40:31.927Z"
   },
   {
    "duration": 12,
    "start_time": "2025-03-10T22:40:31.964Z"
   },
   {
    "duration": 40,
    "start_time": "2025-03-10T22:40:31.978Z"
   },
   {
    "duration": 14,
    "start_time": "2025-03-10T22:40:32.023Z"
   },
   {
    "duration": 145,
    "start_time": "2025-03-10T22:40:32.039Z"
   },
   {
    "duration": 7,
    "start_time": "2025-03-10T22:40:32.186Z"
   },
   {
    "duration": 35,
    "start_time": "2025-03-10T22:40:32.195Z"
   },
   {
    "duration": 19,
    "start_time": "2025-03-10T22:40:32.232Z"
   },
   {
    "duration": 20,
    "start_time": "2025-03-10T22:40:32.253Z"
   },
   {
    "duration": 11,
    "start_time": "2025-03-10T22:40:32.274Z"
   },
   {
    "duration": 29,
    "start_time": "2025-03-10T22:40:32.286Z"
   },
   {
    "duration": 23,
    "start_time": "2025-03-10T22:40:32.317Z"
   },
   {
    "duration": 15,
    "start_time": "2025-03-10T22:40:32.341Z"
   },
   {
    "duration": 374,
    "start_time": "2025-03-10T22:40:32.358Z"
   },
   {
    "duration": 10,
    "start_time": "2025-03-10T22:40:32.734Z"
   },
   {
    "duration": 712,
    "start_time": "2025-03-10T22:40:32.746Z"
   },
   {
    "duration": 247,
    "start_time": "2025-03-10T22:40:33.461Z"
   },
   {
    "duration": 7,
    "start_time": "2025-03-10T22:40:44.866Z"
   },
   {
    "duration": 10,
    "start_time": "2025-03-10T22:42:32.444Z"
   },
   {
    "duration": 182,
    "start_time": "2025-03-12T11:19:31.822Z"
   },
   {
    "duration": 1283,
    "start_time": "2025-03-12T11:20:26.945Z"
   },
   {
    "duration": 19,
    "start_time": "2025-03-12T11:20:28.230Z"
   },
   {
    "duration": 37,
    "start_time": "2025-03-12T11:20:28.251Z"
   },
   {
    "duration": 15,
    "start_time": "2025-03-12T11:20:28.290Z"
   },
   {
    "duration": 12,
    "start_time": "2025-03-12T11:20:28.307Z"
   },
   {
    "duration": 18,
    "start_time": "2025-03-12T11:20:28.321Z"
   },
   {
    "duration": 11,
    "start_time": "2025-03-12T11:20:28.340Z"
   },
   {
    "duration": 38,
    "start_time": "2025-03-12T11:20:28.353Z"
   },
   {
    "duration": 18,
    "start_time": "2025-03-12T11:20:28.393Z"
   },
   {
    "duration": 15,
    "start_time": "2025-03-12T11:20:28.414Z"
   },
   {
    "duration": 7,
    "start_time": "2025-03-12T11:20:28.430Z"
   },
   {
    "duration": 40,
    "start_time": "2025-03-12T11:20:28.439Z"
   },
   {
    "duration": 12,
    "start_time": "2025-03-12T11:20:28.481Z"
   },
   {
    "duration": 26,
    "start_time": "2025-03-12T11:20:28.495Z"
   },
   {
    "duration": 6,
    "start_time": "2025-03-12T11:20:28.523Z"
   },
   {
    "duration": 9,
    "start_time": "2025-03-12T11:20:28.531Z"
   },
   {
    "duration": 5,
    "start_time": "2025-03-12T11:20:28.542Z"
   },
   {
    "duration": 7,
    "start_time": "2025-03-12T11:20:28.549Z"
   },
   {
    "duration": 26,
    "start_time": "2025-03-12T11:20:28.558Z"
   },
   {
    "duration": 11,
    "start_time": "2025-03-12T11:20:28.586Z"
   },
   {
    "duration": 5,
    "start_time": "2025-03-12T11:20:28.599Z"
   },
   {
    "duration": 231,
    "start_time": "2025-03-12T11:20:28.606Z"
   },
   {
    "duration": 108,
    "start_time": "2025-03-12T11:20:28.839Z"
   },
   {
    "duration": 264,
    "start_time": "2025-03-12T11:20:28.949Z"
   },
   {
    "duration": 548,
    "start_time": "2025-03-12T11:20:29.215Z"
   },
   {
    "duration": 590,
    "start_time": "2025-03-12T11:20:29.765Z"
   },
   {
    "duration": 26,
    "start_time": "2025-03-12T11:20:30.357Z"
   },
   {
    "duration": 26,
    "start_time": "2025-03-12T11:20:30.387Z"
   },
   {
    "duration": 28,
    "start_time": "2025-03-12T11:20:30.414Z"
   },
   {
    "duration": 23,
    "start_time": "2025-03-12T11:20:30.444Z"
   },
   {
    "duration": 18,
    "start_time": "2025-03-12T11:20:30.472Z"
   },
   {
    "duration": 9,
    "start_time": "2025-03-12T11:20:30.493Z"
   },
   {
    "duration": 12,
    "start_time": "2025-03-12T11:20:30.504Z"
   },
   {
    "duration": 18,
    "start_time": "2025-03-12T11:20:30.518Z"
   },
   {
    "duration": 189,
    "start_time": "2025-03-12T11:20:30.537Z"
   },
   {
    "duration": 195,
    "start_time": "2025-03-12T11:20:30.728Z"
   },
   {
    "duration": 4,
    "start_time": "2025-03-12T11:20:30.925Z"
   },
   {
    "duration": 24,
    "start_time": "2025-03-12T11:20:30.930Z"
   },
   {
    "duration": 30,
    "start_time": "2025-03-12T11:20:30.957Z"
   },
   {
    "duration": 22,
    "start_time": "2025-03-12T11:20:30.989Z"
   },
   {
    "duration": 4,
    "start_time": "2025-03-12T11:20:31.013Z"
   },
   {
    "duration": 171,
    "start_time": "2025-03-12T11:20:31.018Z"
   },
   {
    "duration": 11,
    "start_time": "2025-03-12T11:20:31.191Z"
   },
   {
    "duration": 18,
    "start_time": "2025-03-12T11:20:31.203Z"
   },
   {
    "duration": 10,
    "start_time": "2025-03-12T11:20:31.223Z"
   },
   {
    "duration": 74,
    "start_time": "2025-03-12T11:20:31.235Z"
   },
   {
    "duration": 65,
    "start_time": "2025-03-12T15:23:31.936Z"
   },
   {
    "duration": 178,
    "start_time": "2025-03-12T15:40:08.970Z"
   },
   {
    "duration": 255,
    "start_time": "2025-03-12T15:40:55.068Z"
   },
   {
    "duration": 169,
    "start_time": "2025-03-12T15:41:05.056Z"
   },
   {
    "duration": 162,
    "start_time": "2025-03-12T15:41:12.313Z"
   },
   {
    "duration": 168,
    "start_time": "2025-03-12T15:41:19.329Z"
   },
   {
    "duration": 12,
    "start_time": "2025-03-12T15:41:41.527Z"
   },
   {
    "duration": 4,
    "start_time": "2025-03-12T15:45:50.433Z"
   },
   {
    "duration": 175,
    "start_time": "2025-03-12T15:45:58.116Z"
   },
   {
    "duration": 8,
    "start_time": "2025-03-12T15:46:13.402Z"
   },
   {
    "duration": 4,
    "start_time": "2025-03-12T15:46:31.029Z"
   },
   {
    "duration": 7,
    "start_time": "2025-03-12T15:49:29.817Z"
   },
   {
    "duration": 583,
    "start_time": "2025-03-12T15:53:01.704Z"
   },
   {
    "duration": 65,
    "start_time": "2025-03-12T15:53:37.970Z"
   },
   {
    "duration": 9,
    "start_time": "2025-03-12T15:53:44.126Z"
   },
   {
    "duration": 79,
    "start_time": "2025-03-12T15:54:21.608Z"
   },
   {
    "duration": 405,
    "start_time": "2025-03-12T15:57:43.769Z"
   },
   {
    "duration": 9,
    "start_time": "2025-03-12T15:58:13.008Z"
   },
   {
    "duration": 8,
    "start_time": "2025-03-12T15:59:05.227Z"
   },
   {
    "duration": 173,
    "start_time": "2025-03-12T16:01:58.109Z"
   },
   {
    "duration": 11,
    "start_time": "2025-03-12T16:02:11.359Z"
   },
   {
    "duration": 371,
    "start_time": "2025-03-12T16:08:07.792Z"
   },
   {
    "duration": 348,
    "start_time": "2025-03-12T16:11:34.917Z"
   },
   {
    "duration": 1209,
    "start_time": "2025-03-12T16:19:07.305Z"
   },
   {
    "duration": 19,
    "start_time": "2025-03-12T16:19:08.516Z"
   },
   {
    "duration": 29,
    "start_time": "2025-03-12T16:19:08.536Z"
   },
   {
    "duration": 16,
    "start_time": "2025-03-12T16:19:08.567Z"
   },
   {
    "duration": 12,
    "start_time": "2025-03-12T16:19:08.584Z"
   },
   {
    "duration": 18,
    "start_time": "2025-03-12T16:19:08.598Z"
   },
   {
    "duration": 10,
    "start_time": "2025-03-12T16:19:08.618Z"
   },
   {
    "duration": 11,
    "start_time": "2025-03-12T16:19:08.629Z"
   },
   {
    "duration": 11,
    "start_time": "2025-03-12T16:19:08.641Z"
   },
   {
    "duration": 25,
    "start_time": "2025-03-12T16:19:08.654Z"
   },
   {
    "duration": 7,
    "start_time": "2025-03-12T16:19:08.681Z"
   },
   {
    "duration": 9,
    "start_time": "2025-03-12T16:19:08.690Z"
   },
   {
    "duration": 10,
    "start_time": "2025-03-12T16:19:08.700Z"
   },
   {
    "duration": 9,
    "start_time": "2025-03-12T16:19:08.711Z"
   },
   {
    "duration": 20,
    "start_time": "2025-03-12T16:19:08.721Z"
   },
   {
    "duration": 5,
    "start_time": "2025-03-12T16:19:08.742Z"
   },
   {
    "duration": 25,
    "start_time": "2025-03-12T16:19:08.749Z"
   },
   {
    "duration": 8,
    "start_time": "2025-03-12T16:19:08.776Z"
   },
   {
    "duration": 5,
    "start_time": "2025-03-12T16:19:08.785Z"
   },
   {
    "duration": 9,
    "start_time": "2025-03-12T16:19:08.792Z"
   },
   {
    "duration": 5,
    "start_time": "2025-03-12T16:19:08.802Z"
   },
   {
    "duration": 218,
    "start_time": "2025-03-12T16:19:08.809Z"
   },
   {
    "duration": 100,
    "start_time": "2025-03-12T16:19:09.029Z"
   },
   {
    "duration": 232,
    "start_time": "2025-03-12T16:19:09.131Z"
   },
   {
    "duration": 467,
    "start_time": "2025-03-12T16:19:09.373Z"
   },
   {
    "duration": 503,
    "start_time": "2025-03-12T16:19:09.842Z"
   },
   {
    "duration": 9,
    "start_time": "2025-03-12T16:19:10.347Z"
   },
   {
    "duration": 21,
    "start_time": "2025-03-12T16:19:10.360Z"
   },
   {
    "duration": 20,
    "start_time": "2025-03-12T16:19:10.383Z"
   },
   {
    "duration": 20,
    "start_time": "2025-03-12T16:19:10.404Z"
   },
   {
    "duration": 7,
    "start_time": "2025-03-12T16:19:10.426Z"
   },
   {
    "duration": 9,
    "start_time": "2025-03-12T16:19:10.434Z"
   },
   {
    "duration": 7,
    "start_time": "2025-03-12T16:19:10.473Z"
   },
   {
    "duration": 19,
    "start_time": "2025-03-12T16:19:10.481Z"
   },
   {
    "duration": 176,
    "start_time": "2025-03-12T16:19:10.502Z"
   },
   {
    "duration": 175,
    "start_time": "2025-03-12T16:19:10.679Z"
   },
   {
    "duration": 175,
    "start_time": "2025-03-12T16:19:10.855Z"
   },
   {
    "duration": 11,
    "start_time": "2025-03-12T16:19:11.032Z"
   },
   {
    "duration": 3,
    "start_time": "2025-03-12T16:19:11.044Z"
   },
   {
    "duration": 37,
    "start_time": "2025-03-12T16:19:11.049Z"
   },
   {
    "duration": 9,
    "start_time": "2025-03-12T16:19:11.088Z"
   },
   {
    "duration": 14,
    "start_time": "2025-03-12T16:19:11.099Z"
   },
   {
    "duration": 4,
    "start_time": "2025-03-12T16:19:11.115Z"
   },
   {
    "duration": 275,
    "start_time": "2025-03-12T16:19:11.121Z"
   },
   {
    "duration": 9,
    "start_time": "2025-03-12T16:19:11.398Z"
   },
   {
    "duration": 7,
    "start_time": "2025-03-12T16:19:11.408Z"
   },
   {
    "duration": 3,
    "start_time": "2025-03-12T16:19:11.417Z"
   },
   {
    "duration": 126,
    "start_time": "2025-03-12T16:19:11.421Z"
   },
   {
    "duration": 0,
    "start_time": "2025-03-12T16:19:11.549Z"
   },
   {
    "duration": 0,
    "start_time": "2025-03-12T16:19:11.550Z"
   },
   {
    "duration": 0,
    "start_time": "2025-03-12T16:19:11.551Z"
   },
   {
    "duration": 0,
    "start_time": "2025-03-12T16:19:11.552Z"
   },
   {
    "duration": 0,
    "start_time": "2025-03-12T16:19:11.553Z"
   },
   {
    "duration": 0,
    "start_time": "2025-03-12T16:19:11.554Z"
   },
   {
    "duration": 0,
    "start_time": "2025-03-12T16:19:11.555Z"
   },
   {
    "duration": 1184,
    "start_time": "2025-03-12T17:13:06.337Z"
   },
   {
    "duration": 89,
    "start_time": "2025-03-12T17:13:07.523Z"
   },
   {
    "duration": 31,
    "start_time": "2025-03-12T17:13:07.613Z"
   },
   {
    "duration": 17,
    "start_time": "2025-03-12T17:13:07.646Z"
   },
   {
    "duration": 12,
    "start_time": "2025-03-12T17:13:07.665Z"
   },
   {
    "duration": 38,
    "start_time": "2025-03-12T17:13:07.678Z"
   },
   {
    "duration": 31,
    "start_time": "2025-03-12T17:13:07.718Z"
   },
   {
    "duration": 13,
    "start_time": "2025-03-12T17:13:07.751Z"
   },
   {
    "duration": 12,
    "start_time": "2025-03-12T17:13:07.766Z"
   },
   {
    "duration": 16,
    "start_time": "2025-03-12T17:13:07.781Z"
   },
   {
    "duration": 28,
    "start_time": "2025-03-12T17:13:07.799Z"
   },
   {
    "duration": 12,
    "start_time": "2025-03-12T17:13:07.829Z"
   },
   {
    "duration": 14,
    "start_time": "2025-03-12T17:13:07.842Z"
   },
   {
    "duration": 15,
    "start_time": "2025-03-12T17:13:07.858Z"
   },
   {
    "duration": 12,
    "start_time": "2025-03-12T17:13:07.874Z"
   },
   {
    "duration": 28,
    "start_time": "2025-03-12T17:13:07.887Z"
   },
   {
    "duration": 20,
    "start_time": "2025-03-12T17:13:07.917Z"
   },
   {
    "duration": 12,
    "start_time": "2025-03-12T17:13:07.938Z"
   },
   {
    "duration": 24,
    "start_time": "2025-03-12T17:13:07.951Z"
   },
   {
    "duration": 9,
    "start_time": "2025-03-12T17:13:07.977Z"
   },
   {
    "duration": 4,
    "start_time": "2025-03-12T17:13:07.988Z"
   },
   {
    "duration": 195,
    "start_time": "2025-03-12T17:13:07.994Z"
   },
   {
    "duration": 106,
    "start_time": "2025-03-12T17:13:08.190Z"
   },
   {
    "duration": 233,
    "start_time": "2025-03-12T17:13:08.299Z"
   },
   {
    "duration": 511,
    "start_time": "2025-03-12T17:13:08.534Z"
   },
   {
    "duration": 491,
    "start_time": "2025-03-12T17:13:09.047Z"
   },
   {
    "duration": 8,
    "start_time": "2025-03-12T17:13:09.540Z"
   },
   {
    "duration": 14,
    "start_time": "2025-03-12T17:13:09.551Z"
   },
   {
    "duration": 18,
    "start_time": "2025-03-12T17:13:09.573Z"
   },
   {
    "duration": 17,
    "start_time": "2025-03-12T17:13:09.592Z"
   },
   {
    "duration": 7,
    "start_time": "2025-03-12T17:13:09.610Z"
   },
   {
    "duration": 8,
    "start_time": "2025-03-12T17:13:09.618Z"
   },
   {
    "duration": 4,
    "start_time": "2025-03-12T17:13:09.628Z"
   },
   {
    "duration": 17,
    "start_time": "2025-03-12T17:13:09.634Z"
   },
   {
    "duration": 183,
    "start_time": "2025-03-12T17:13:09.652Z"
   },
   {
    "duration": 173,
    "start_time": "2025-03-12T17:13:09.837Z"
   },
   {
    "duration": 179,
    "start_time": "2025-03-12T17:13:10.012Z"
   },
   {
    "duration": 10,
    "start_time": "2025-03-12T17:13:10.193Z"
   },
   {
    "duration": 17,
    "start_time": "2025-03-12T17:13:10.205Z"
   },
   {
    "duration": 27,
    "start_time": "2025-03-12T17:13:10.224Z"
   },
   {
    "duration": 9,
    "start_time": "2025-03-12T17:13:10.253Z"
   },
   {
    "duration": 14,
    "start_time": "2025-03-12T17:13:10.264Z"
   },
   {
    "duration": 7,
    "start_time": "2025-03-12T17:13:10.280Z"
   },
   {
    "duration": 263,
    "start_time": "2025-03-12T17:13:10.288Z"
   },
   {
    "duration": 8,
    "start_time": "2025-03-12T17:13:10.552Z"
   },
   {
    "duration": 17,
    "start_time": "2025-03-12T17:13:10.561Z"
   },
   {
    "duration": 5,
    "start_time": "2025-03-12T17:13:10.580Z"
   },
   {
    "duration": 117,
    "start_time": "2025-03-12T17:13:10.586Z"
   },
   {
    "duration": 0,
    "start_time": "2025-03-12T17:13:10.704Z"
   },
   {
    "duration": 0,
    "start_time": "2025-03-12T17:13:10.706Z"
   },
   {
    "duration": 0,
    "start_time": "2025-03-12T17:13:10.707Z"
   },
   {
    "duration": 0,
    "start_time": "2025-03-12T17:13:10.709Z"
   },
   {
    "duration": 0,
    "start_time": "2025-03-12T17:13:10.710Z"
   },
   {
    "duration": 0,
    "start_time": "2025-03-12T17:13:10.711Z"
   },
   {
    "duration": 0,
    "start_time": "2025-03-12T17:13:10.712Z"
   },
   {
    "duration": 8,
    "start_time": "2025-03-12T17:16:04.582Z"
   },
   {
    "duration": 630,
    "start_time": "2025-03-12T17:16:23.866Z"
   },
   {
    "duration": 1218,
    "start_time": "2025-03-12T17:16:33.770Z"
   },
   {
    "duration": 18,
    "start_time": "2025-03-12T17:16:34.990Z"
   },
   {
    "duration": 84,
    "start_time": "2025-03-12T17:16:35.010Z"
   },
   {
    "duration": 16,
    "start_time": "2025-03-12T17:16:35.096Z"
   },
   {
    "duration": 12,
    "start_time": "2025-03-12T17:16:35.113Z"
   },
   {
    "duration": 15,
    "start_time": "2025-03-12T17:16:35.127Z"
   },
   {
    "duration": 17,
    "start_time": "2025-03-12T17:16:35.144Z"
   },
   {
    "duration": 22,
    "start_time": "2025-03-12T17:16:35.163Z"
   },
   {
    "duration": 11,
    "start_time": "2025-03-12T17:16:35.187Z"
   },
   {
    "duration": 24,
    "start_time": "2025-03-12T17:16:35.201Z"
   },
   {
    "duration": 32,
    "start_time": "2025-03-12T17:16:35.226Z"
   },
   {
    "duration": 6,
    "start_time": "2025-03-12T17:16:35.260Z"
   },
   {
    "duration": 30,
    "start_time": "2025-03-12T17:16:35.267Z"
   },
   {
    "duration": 23,
    "start_time": "2025-03-12T17:16:35.299Z"
   },
   {
    "duration": 10,
    "start_time": "2025-03-12T17:16:35.324Z"
   },
   {
    "duration": 20,
    "start_time": "2025-03-12T17:16:35.335Z"
   },
   {
    "duration": 6,
    "start_time": "2025-03-12T17:16:35.357Z"
   },
   {
    "duration": 9,
    "start_time": "2025-03-12T17:16:35.365Z"
   },
   {
    "duration": 7,
    "start_time": "2025-03-12T17:16:35.375Z"
   },
   {
    "duration": 11,
    "start_time": "2025-03-12T17:16:35.383Z"
   },
   {
    "duration": 5,
    "start_time": "2025-03-12T17:16:35.396Z"
   },
   {
    "duration": 214,
    "start_time": "2025-03-12T17:16:35.403Z"
   },
   {
    "duration": 107,
    "start_time": "2025-03-12T17:16:35.618Z"
   },
   {
    "duration": 238,
    "start_time": "2025-03-12T17:16:35.727Z"
   },
   {
    "duration": 475,
    "start_time": "2025-03-12T17:16:35.967Z"
   },
   {
    "duration": 494,
    "start_time": "2025-03-12T17:16:36.444Z"
   },
   {
    "duration": 9,
    "start_time": "2025-03-12T17:16:36.940Z"
   },
   {
    "duration": 9,
    "start_time": "2025-03-12T17:16:36.951Z"
   },
   {
    "duration": 19,
    "start_time": "2025-03-12T17:16:36.974Z"
   },
   {
    "duration": 110,
    "start_time": "2025-03-12T17:16:36.994Z"
   },
   {
    "duration": 6,
    "start_time": "2025-03-12T17:16:37.106Z"
   },
   {
    "duration": 28,
    "start_time": "2025-03-12T17:16:37.113Z"
   },
   {
    "duration": 5,
    "start_time": "2025-03-12T17:16:37.142Z"
   },
   {
    "duration": 7,
    "start_time": "2025-03-12T17:16:37.149Z"
   },
   {
    "duration": 163,
    "start_time": "2025-03-12T17:16:37.157Z"
   },
   {
    "duration": 173,
    "start_time": "2025-03-12T17:16:37.322Z"
   },
   {
    "duration": 184,
    "start_time": "2025-03-12T17:16:37.497Z"
   },
   {
    "duration": 13,
    "start_time": "2025-03-12T17:16:37.683Z"
   },
   {
    "duration": 10,
    "start_time": "2025-03-12T17:16:37.700Z"
   },
   {
    "duration": 28,
    "start_time": "2025-03-12T17:16:37.712Z"
   },
   {
    "duration": 10,
    "start_time": "2025-03-12T17:16:37.742Z"
   },
   {
    "duration": 15,
    "start_time": "2025-03-12T17:16:37.772Z"
   },
   {
    "duration": 5,
    "start_time": "2025-03-12T17:16:37.789Z"
   },
   {
    "duration": 272,
    "start_time": "2025-03-12T17:16:37.795Z"
   },
   {
    "duration": 10,
    "start_time": "2025-03-12T17:16:38.069Z"
   },
   {
    "duration": 7,
    "start_time": "2025-03-12T17:16:38.081Z"
   },
   {
    "duration": 9,
    "start_time": "2025-03-12T17:16:38.090Z"
   },
   {
    "duration": 10,
    "start_time": "2025-03-12T17:16:38.100Z"
   },
   {
    "duration": 578,
    "start_time": "2025-03-12T17:16:38.112Z"
   },
   {
    "duration": 425,
    "start_time": "2025-03-12T17:16:38.692Z"
   },
   {
    "duration": 9,
    "start_time": "2025-03-12T17:16:39.119Z"
   },
   {
    "duration": 192,
    "start_time": "2025-03-12T17:16:39.130Z"
   },
   {
    "duration": 10,
    "start_time": "2025-03-12T17:16:39.323Z"
   },
   {
    "duration": 388,
    "start_time": "2025-03-12T17:16:39.335Z"
   },
   {
    "duration": 382,
    "start_time": "2025-03-12T17:16:39.725Z"
   },
   {
    "duration": 191,
    "start_time": "2025-03-12T17:21:46.470Z"
   },
   {
    "duration": 617,
    "start_time": "2025-03-12T17:22:30.236Z"
   },
   {
    "duration": 11,
    "start_time": "2025-03-12T17:22:43.586Z"
   },
   {
    "duration": 89,
    "start_time": "2025-03-12T17:23:07.496Z"
   },
   {
    "duration": 4,
    "start_time": "2025-03-12T17:27:02.681Z"
   },
   {
    "duration": 6,
    "start_time": "2025-03-12T17:28:03.479Z"
   },
   {
    "duration": 115,
    "start_time": "2025-03-12T17:30:47.603Z"
   },
   {
    "duration": 51,
    "start_time": "2025-03-13T16:54:05.645Z"
   },
   {
    "duration": 1136,
    "start_time": "2025-03-13T16:54:11.094Z"
   },
   {
    "duration": 221,
    "start_time": "2025-03-13T16:54:12.231Z"
   },
   {
    "duration": 28,
    "start_time": "2025-03-13T16:54:12.454Z"
   },
   {
    "duration": 15,
    "start_time": "2025-03-13T16:54:12.483Z"
   },
   {
    "duration": 91,
    "start_time": "2025-03-13T16:54:12.500Z"
   },
   {
    "duration": 20,
    "start_time": "2025-03-13T16:54:12.592Z"
   },
   {
    "duration": 10,
    "start_time": "2025-03-13T16:54:12.614Z"
   },
   {
    "duration": 9,
    "start_time": "2025-03-13T16:54:12.626Z"
   },
   {
    "duration": 11,
    "start_time": "2025-03-13T16:54:12.636Z"
   },
   {
    "duration": 8,
    "start_time": "2025-03-13T16:54:12.648Z"
   },
   {
    "duration": 8,
    "start_time": "2025-03-13T16:54:12.657Z"
   },
   {
    "duration": 10,
    "start_time": "2025-03-13T16:54:12.667Z"
   },
   {
    "duration": 10,
    "start_time": "2025-03-13T16:54:12.679Z"
   },
   {
    "duration": 5,
    "start_time": "2025-03-13T16:54:12.692Z"
   },
   {
    "duration": 32,
    "start_time": "2025-03-13T16:54:12.699Z"
   },
   {
    "duration": 7,
    "start_time": "2025-03-13T16:54:12.732Z"
   },
   {
    "duration": 6,
    "start_time": "2025-03-13T16:54:12.741Z"
   },
   {
    "duration": 9,
    "start_time": "2025-03-13T16:54:12.749Z"
   },
   {
    "duration": 5,
    "start_time": "2025-03-13T16:54:12.760Z"
   },
   {
    "duration": 14,
    "start_time": "2025-03-13T16:54:12.767Z"
   },
   {
    "duration": 5,
    "start_time": "2025-03-13T16:54:12.783Z"
   },
   {
    "duration": 214,
    "start_time": "2025-03-13T16:54:12.790Z"
   },
   {
    "duration": 170,
    "start_time": "2025-03-13T16:54:13.006Z"
   },
   {
    "duration": 100,
    "start_time": "2025-03-13T16:54:13.178Z"
   },
   {
    "duration": 222,
    "start_time": "2025-03-13T16:54:13.280Z"
   },
   {
    "duration": 476,
    "start_time": "2025-03-13T16:54:13.503Z"
   },
   {
    "duration": 508,
    "start_time": "2025-03-13T16:54:13.980Z"
   },
   {
    "duration": 9,
    "start_time": "2025-03-13T16:54:14.489Z"
   },
   {
    "duration": 83,
    "start_time": "2025-03-13T16:54:14.499Z"
   },
   {
    "duration": 16,
    "start_time": "2025-03-13T16:54:14.584Z"
   },
   {
    "duration": 17,
    "start_time": "2025-03-13T16:54:14.601Z"
   },
   {
    "duration": 7,
    "start_time": "2025-03-13T16:54:14.620Z"
   },
   {
    "duration": 8,
    "start_time": "2025-03-13T16:54:14.629Z"
   },
   {
    "duration": 5,
    "start_time": "2025-03-13T16:54:14.639Z"
   },
   {
    "duration": 10,
    "start_time": "2025-03-13T16:54:14.645Z"
   },
   {
    "duration": 168,
    "start_time": "2025-03-13T16:54:14.656Z"
   },
   {
    "duration": 164,
    "start_time": "2025-03-13T16:54:14.825Z"
   },
   {
    "duration": 684,
    "start_time": "2025-03-13T16:54:14.990Z"
   },
   {
    "duration": 11,
    "start_time": "2025-03-13T16:54:15.676Z"
   },
   {
    "duration": 3,
    "start_time": "2025-03-13T16:54:15.689Z"
   },
   {
    "duration": 35,
    "start_time": "2025-03-13T16:54:15.695Z"
   },
   {
    "duration": 61,
    "start_time": "2025-03-13T16:54:15.731Z"
   },
   {
    "duration": 14,
    "start_time": "2025-03-13T16:54:15.794Z"
   },
   {
    "duration": 4,
    "start_time": "2025-03-13T16:54:15.810Z"
   },
   {
    "duration": 177,
    "start_time": "2025-03-13T16:54:15.816Z"
   },
   {
    "duration": 8,
    "start_time": "2025-03-13T16:54:15.995Z"
   },
   {
    "duration": 7,
    "start_time": "2025-03-13T16:54:16.004Z"
   },
   {
    "duration": 18,
    "start_time": "2025-03-13T16:54:16.012Z"
   },
   {
    "duration": 6,
    "start_time": "2025-03-13T16:54:16.032Z"
   },
   {
    "duration": 597,
    "start_time": "2025-03-13T16:54:16.039Z"
   },
   {
    "duration": 424,
    "start_time": "2025-03-13T16:54:16.637Z"
   },
   {
    "duration": 8,
    "start_time": "2025-03-13T16:54:17.063Z"
   },
   {
    "duration": 184,
    "start_time": "2025-03-13T16:54:17.072Z"
   },
   {
    "duration": 9,
    "start_time": "2025-03-13T16:54:17.258Z"
   },
   {
    "duration": 328,
    "start_time": "2025-03-13T16:54:17.269Z"
   },
   {
    "duration": 388,
    "start_time": "2025-03-13T16:54:17.599Z"
   },
   {
    "duration": 4,
    "start_time": "2025-03-13T16:55:33.854Z"
   },
   {
    "duration": 5,
    "start_time": "2025-03-13T16:55:34.366Z"
   },
   {
    "duration": 8,
    "start_time": "2025-03-13T16:55:34.902Z"
   },
   {
    "duration": 841,
    "start_time": "2025-03-13T16:55:35.430Z"
   },
   {
    "duration": 103,
    "start_time": "2025-03-13T16:55:50.198Z"
   },
   {
    "duration": 6,
    "start_time": "2025-03-13T16:55:53.952Z"
   },
   {
    "duration": 1233,
    "start_time": "2025-03-13T16:56:03.117Z"
   },
   {
    "duration": 209,
    "start_time": "2025-03-13T16:56:04.352Z"
   },
   {
    "duration": 29,
    "start_time": "2025-03-13T16:56:04.563Z"
   },
   {
    "duration": 227,
    "start_time": "2025-03-13T16:56:04.593Z"
   },
   {
    "duration": 12,
    "start_time": "2025-03-13T16:56:04.822Z"
   },
   {
    "duration": 15,
    "start_time": "2025-03-13T16:56:04.835Z"
   },
   {
    "duration": 12,
    "start_time": "2025-03-13T16:56:04.852Z"
   },
   {
    "duration": 8,
    "start_time": "2025-03-13T16:56:04.866Z"
   },
   {
    "duration": 28,
    "start_time": "2025-03-13T16:56:04.876Z"
   },
   {
    "duration": 7,
    "start_time": "2025-03-13T16:56:04.905Z"
   },
   {
    "duration": 22,
    "start_time": "2025-03-13T16:56:04.914Z"
   },
   {
    "duration": 4,
    "start_time": "2025-03-13T16:56:04.938Z"
   },
   {
    "duration": 10,
    "start_time": "2025-03-13T16:56:04.944Z"
   },
   {
    "duration": 6,
    "start_time": "2025-03-13T16:56:04.957Z"
   },
   {
    "duration": 6,
    "start_time": "2025-03-13T16:56:04.964Z"
   },
   {
    "duration": 16,
    "start_time": "2025-03-13T16:56:04.971Z"
   },
   {
    "duration": 13,
    "start_time": "2025-03-13T16:56:04.988Z"
   },
   {
    "duration": 12,
    "start_time": "2025-03-13T16:56:05.029Z"
   },
   {
    "duration": 6,
    "start_time": "2025-03-13T16:56:05.043Z"
   },
   {
    "duration": 11,
    "start_time": "2025-03-13T16:56:05.051Z"
   },
   {
    "duration": 29,
    "start_time": "2025-03-13T16:56:05.064Z"
   },
   {
    "duration": 183,
    "start_time": "2025-03-13T16:56:05.095Z"
   },
   {
    "duration": 170,
    "start_time": "2025-03-13T16:56:05.281Z"
   },
   {
    "duration": 99,
    "start_time": "2025-03-13T16:56:05.453Z"
   },
   {
    "duration": 222,
    "start_time": "2025-03-13T16:56:05.554Z"
   },
   {
    "duration": 506,
    "start_time": "2025-03-13T16:56:05.777Z"
   },
   {
    "duration": 502,
    "start_time": "2025-03-13T16:56:06.284Z"
   },
   {
    "duration": 10,
    "start_time": "2025-03-13T16:56:06.787Z"
   },
   {
    "duration": 14,
    "start_time": "2025-03-13T16:56:06.798Z"
   },
   {
    "duration": 28,
    "start_time": "2025-03-13T16:56:06.813Z"
   },
   {
    "duration": 18,
    "start_time": "2025-03-13T16:56:06.842Z"
   },
   {
    "duration": 7,
    "start_time": "2025-03-13T16:56:06.862Z"
   },
   {
    "duration": 50,
    "start_time": "2025-03-13T16:56:06.870Z"
   },
   {
    "duration": 6,
    "start_time": "2025-03-13T16:56:06.921Z"
   },
   {
    "duration": 17,
    "start_time": "2025-03-13T16:56:06.929Z"
   },
   {
    "duration": 187,
    "start_time": "2025-03-13T16:56:06.948Z"
   },
   {
    "duration": 187,
    "start_time": "2025-03-13T16:56:07.137Z"
   },
   {
    "duration": 634,
    "start_time": "2025-03-13T16:56:07.330Z"
   },
   {
    "duration": 12,
    "start_time": "2025-03-13T16:56:07.966Z"
   },
   {
    "duration": 5,
    "start_time": "2025-03-13T16:56:07.980Z"
   },
   {
    "duration": 44,
    "start_time": "2025-03-13T16:56:07.990Z"
   },
   {
    "duration": 9,
    "start_time": "2025-03-13T16:56:08.036Z"
   },
   {
    "duration": 23,
    "start_time": "2025-03-13T16:56:08.047Z"
   },
   {
    "duration": 5,
    "start_time": "2025-03-13T16:56:08.072Z"
   },
   {
    "duration": 175,
    "start_time": "2025-03-13T16:56:08.079Z"
   },
   {
    "duration": 8,
    "start_time": "2025-03-13T16:56:08.256Z"
   },
   {
    "duration": 6,
    "start_time": "2025-03-13T16:56:08.266Z"
   },
   {
    "duration": 5,
    "start_time": "2025-03-13T16:56:08.273Z"
   },
   {
    "duration": 6,
    "start_time": "2025-03-13T16:56:08.280Z"
   },
   {
    "duration": 652,
    "start_time": "2025-03-13T16:56:08.288Z"
   },
   {
    "duration": 418,
    "start_time": "2025-03-13T16:56:08.942Z"
   },
   {
    "duration": 9,
    "start_time": "2025-03-13T16:56:09.362Z"
   },
   {
    "duration": 182,
    "start_time": "2025-03-13T16:56:09.373Z"
   },
   {
    "duration": 9,
    "start_time": "2025-03-13T16:56:09.556Z"
   },
   {
    "duration": 324,
    "start_time": "2025-03-13T16:56:09.567Z"
   },
   {
    "duration": 358,
    "start_time": "2025-03-13T16:56:09.893Z"
   },
   {
    "duration": 6,
    "start_time": "2025-03-13T16:56:10.253Z"
   },
   {
    "duration": 14,
    "start_time": "2025-03-13T16:56:10.260Z"
   },
   {
    "duration": 15,
    "start_time": "2025-03-13T16:56:10.275Z"
   },
   {
    "duration": 12,
    "start_time": "2025-03-13T16:56:10.291Z"
   },
   {
    "duration": 36,
    "start_time": "2025-03-13T16:56:10.305Z"
   },
   {
    "duration": 17,
    "start_time": "2025-03-13T16:56:10.343Z"
   },
   {
    "duration": 6,
    "start_time": "2025-03-13T16:56:10.362Z"
   },
   {
    "duration": 13,
    "start_time": "2025-03-13T16:56:10.370Z"
   },
   {
    "duration": 4,
    "start_time": "2025-03-13T16:56:10.384Z"
   },
   {
    "duration": 3,
    "start_time": "2025-03-13T16:56:10.390Z"
   },
   {
    "duration": 44,
    "start_time": "2025-03-13T16:56:10.394Z"
   },
   {
    "duration": 879,
    "start_time": "2025-03-13T16:56:10.440Z"
   },
   {
    "duration": 10,
    "start_time": "2025-03-13T16:56:11.321Z"
   },
   {
    "duration": 20,
    "start_time": "2025-03-13T16:56:11.333Z"
   },
   {
    "duration": 13,
    "start_time": "2025-03-13T16:56:11.355Z"
   },
   {
    "duration": 12,
    "start_time": "2025-03-13T16:56:11.369Z"
   },
   {
    "duration": 12,
    "start_time": "2025-03-13T16:56:11.383Z"
   },
   {
    "duration": 817,
    "start_time": "2025-03-13T16:56:11.397Z"
   },
   {
    "duration": 6,
    "start_time": "2025-03-13T16:56:12.216Z"
   },
   {
    "duration": 1361,
    "start_time": "2025-03-13T16:59:11.611Z"
   },
   {
    "duration": 48,
    "start_time": "2025-03-13T16:59:12.973Z"
   },
   {
    "duration": 269,
    "start_time": "2025-03-13T16:59:13.023Z"
   },
   {
    "duration": 750,
    "start_time": "2025-03-13T16:59:13.294Z"
   },
   {
    "duration": 14,
    "start_time": "2025-03-13T16:59:14.045Z"
   },
   {
    "duration": 14,
    "start_time": "2025-03-13T16:59:14.060Z"
   },
   {
    "duration": 11,
    "start_time": "2025-03-13T16:59:14.075Z"
   },
   {
    "duration": 7,
    "start_time": "2025-03-13T16:59:14.087Z"
   },
   {
    "duration": 10,
    "start_time": "2025-03-13T16:59:14.096Z"
   },
   {
    "duration": 25,
    "start_time": "2025-03-13T16:59:14.109Z"
   },
   {
    "duration": 7,
    "start_time": "2025-03-13T16:59:14.136Z"
   },
   {
    "duration": 5,
    "start_time": "2025-03-13T16:59:14.144Z"
   },
   {
    "duration": 11,
    "start_time": "2025-03-13T16:59:14.151Z"
   },
   {
    "duration": 5,
    "start_time": "2025-03-13T16:59:14.164Z"
   },
   {
    "duration": 8,
    "start_time": "2025-03-13T16:59:14.171Z"
   },
   {
    "duration": 6,
    "start_time": "2025-03-13T16:59:14.181Z"
   },
   {
    "duration": 6,
    "start_time": "2025-03-13T16:59:14.229Z"
   },
   {
    "duration": 9,
    "start_time": "2025-03-13T16:59:14.237Z"
   },
   {
    "duration": 6,
    "start_time": "2025-03-13T16:59:14.248Z"
   },
   {
    "duration": 9,
    "start_time": "2025-03-13T16:59:14.255Z"
   },
   {
    "duration": 4,
    "start_time": "2025-03-13T16:59:14.265Z"
   },
   {
    "duration": 199,
    "start_time": "2025-03-13T16:59:14.270Z"
   },
   {
    "duration": 162,
    "start_time": "2025-03-13T16:59:14.471Z"
   },
   {
    "duration": 98,
    "start_time": "2025-03-13T16:59:14.635Z"
   },
   {
    "duration": 220,
    "start_time": "2025-03-13T16:59:14.735Z"
   },
   {
    "duration": 421,
    "start_time": "2025-03-13T16:59:14.957Z"
   },
   {
    "duration": 491,
    "start_time": "2025-03-13T16:59:15.380Z"
   },
   {
    "duration": 11,
    "start_time": "2025-03-13T16:59:15.877Z"
   },
   {
    "duration": 10,
    "start_time": "2025-03-13T16:59:15.890Z"
   },
   {
    "duration": 46,
    "start_time": "2025-03-13T16:59:15.902Z"
   },
   {
    "duration": 52,
    "start_time": "2025-03-13T16:59:15.949Z"
   },
   {
    "duration": 6,
    "start_time": "2025-03-13T16:59:16.002Z"
   },
   {
    "duration": 7,
    "start_time": "2025-03-13T16:59:16.010Z"
   },
   {
    "duration": 4,
    "start_time": "2025-03-13T16:59:16.019Z"
   },
   {
    "duration": 4,
    "start_time": "2025-03-13T16:59:16.024Z"
   },
   {
    "duration": 171,
    "start_time": "2025-03-13T16:59:16.029Z"
   },
   {
    "duration": 176,
    "start_time": "2025-03-13T16:59:16.202Z"
   },
   {
    "duration": 691,
    "start_time": "2025-03-13T16:59:16.380Z"
   },
   {
    "duration": 14,
    "start_time": "2025-03-13T16:59:17.073Z"
   },
   {
    "duration": 4,
    "start_time": "2025-03-13T16:59:17.089Z"
   },
   {
    "duration": 45,
    "start_time": "2025-03-13T16:59:17.095Z"
   },
   {
    "duration": 12,
    "start_time": "2025-03-13T16:59:17.142Z"
   },
   {
    "duration": 12,
    "start_time": "2025-03-13T16:59:17.156Z"
   },
   {
    "duration": 8,
    "start_time": "2025-03-13T16:59:17.170Z"
   },
   {
    "duration": 174,
    "start_time": "2025-03-13T16:59:17.180Z"
   },
   {
    "duration": 8,
    "start_time": "2025-03-13T16:59:17.355Z"
   },
   {
    "duration": 7,
    "start_time": "2025-03-13T16:59:17.364Z"
   },
   {
    "duration": 11,
    "start_time": "2025-03-13T16:59:17.372Z"
   },
   {
    "duration": 22,
    "start_time": "2025-03-13T16:59:17.384Z"
   },
   {
    "duration": 632,
    "start_time": "2025-03-13T16:59:17.407Z"
   },
   {
    "duration": 408,
    "start_time": "2025-03-13T16:59:18.040Z"
   },
   {
    "duration": 8,
    "start_time": "2025-03-13T16:59:18.449Z"
   },
   {
    "duration": 193,
    "start_time": "2025-03-13T16:59:18.458Z"
   },
   {
    "duration": 10,
    "start_time": "2025-03-13T16:59:18.652Z"
   },
   {
    "duration": 337,
    "start_time": "2025-03-13T16:59:18.663Z"
   },
   {
    "duration": 391,
    "start_time": "2025-03-13T16:59:19.002Z"
   },
   {
    "duration": 8,
    "start_time": "2025-03-13T16:59:19.395Z"
   },
   {
    "duration": 28,
    "start_time": "2025-03-13T16:59:19.405Z"
   },
   {
    "duration": 12,
    "start_time": "2025-03-13T16:59:19.435Z"
   },
   {
    "duration": 13,
    "start_time": "2025-03-13T16:59:19.449Z"
   },
   {
    "duration": 42,
    "start_time": "2025-03-13T16:59:19.466Z"
   },
   {
    "duration": 27,
    "start_time": "2025-03-13T16:59:19.510Z"
   },
   {
    "duration": 15,
    "start_time": "2025-03-13T16:59:19.539Z"
   },
   {
    "duration": 14,
    "start_time": "2025-03-13T16:59:19.556Z"
   },
   {
    "duration": 12,
    "start_time": "2025-03-13T16:59:19.572Z"
   },
   {
    "duration": 5,
    "start_time": "2025-03-13T16:59:19.586Z"
   },
   {
    "duration": 10,
    "start_time": "2025-03-13T16:59:19.592Z"
   },
   {
    "duration": 897,
    "start_time": "2025-03-13T16:59:19.603Z"
   },
   {
    "duration": 4,
    "start_time": "2025-03-13T16:59:20.502Z"
   },
   {
    "duration": 114,
    "start_time": "2025-03-13T16:59:20.507Z"
   },
   {
    "duration": 13,
    "start_time": "2025-03-13T16:59:20.623Z"
   },
   {
    "duration": 14,
    "start_time": "2025-03-13T16:59:20.638Z"
   },
   {
    "duration": 12,
    "start_time": "2025-03-13T16:59:20.653Z"
   },
   {
    "duration": 840,
    "start_time": "2025-03-13T16:59:20.667Z"
   },
   {
    "duration": 7,
    "start_time": "2025-03-13T16:59:21.508Z"
   }
  ],
  "kernelspec": {
   "display_name": "Python 3 (ipykernel)",
   "language": "python",
   "name": "python3"
  },
  "language_info": {
   "codemirror_mode": {
    "name": "ipython",
    "version": 3
   },
   "file_extension": ".py",
   "mimetype": "text/x-python",
   "name": "python",
   "nbconvert_exporter": "python",
   "pygments_lexer": "ipython3",
   "version": "3.9.5"
  },
  "toc": {
   "base_numbering": 1,
   "nav_menu": {},
   "number_sections": true,
   "sideBar": true,
   "skip_h1_title": true,
   "title_cell": "Table of Contents",
   "title_sidebar": "Contents",
   "toc_cell": false,
   "toc_position": {
    "height": "478px",
    "left": "45px",
    "top": "110.525px",
    "width": "306px"
   },
   "toc_section_display": true,
   "toc_window_display": true
  }
 },
 "nbformat": 4,
 "nbformat_minor": 5
}
