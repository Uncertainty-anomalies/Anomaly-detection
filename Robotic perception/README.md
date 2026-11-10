# Модель 3 · Неизвестные предметы в складском контейнере

Разведочный анализ данных для чекпойнта №2, Research-трек. Автор: Максимилиан Казаков.

- [EDA.ipynb](EDA.ipynb) — выполненный ноутбук: выбор датасета, проверка данных и разметки, визуализации, проверка аугментаций, простой бейзлайн и выводы.
- [Практическое_значение.pdf](Практическое_значение.pdf) — сценарий применения, риски и критерии оценки для складского пилота.

## Данные

Используем [WISDOM-Real](https://sites.google.com/view/wisdom-dataset/dataset_links) (UC Berkeley, компактный архив 555 МБ). Авторы снимали предметы в контейнерах двумя RGB-D сенсорами и использовали разные физические предметы в обучающей и тестовой группах. Рамки для детекции вычисляются из пиксельных масок видимых экземпляров.

В публичной разметке нет семантических меток и постоянных ID товаров. Эксперимент измеряет, как детектор локализует ранее не встречавшиеся предметы. Для оценки различения известных и неизвестных товаров в одном кадре понадобится дополнительный набор с разметкой SKU и смешанными сценами.

Изображения и аннотации: UC Berkeley, [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). Источник: [Danielczuk и др., *Segmenting Unknown 3D Objects from Real Depth Images using Mask R-CNN Trained on Synthetic Data*](https://arxiv.org/abs/1809.05825). На иллюстрации в ноутбуке нанесены рамки и, где указано, применены аугментации.

## Как воспроизвести

Для чтения сохранённых результатов скачивать данные не требуется. Для повторного запуска откройте терминал в корне репозитория и выполните:

```bash
cd "Robotic perception"
python3 -m venv .venv
source .venv/bin/activate
python -m pip install "numpy>=1.26,<3" "pandas>=2.2,<3" \
  "opencv-python-headless>=4.8,<5" "matplotlib>=3.8,<4" "seaborn>=0.13,<0.14" "jupyter>=1,<2"

mkdir -p data
curl --fail --location --retry 2 \
  'https://drive.usercontent.google.com/download?id=1-llS4fyJ2WIHx06_VdHd6uyyN6n02CHJ&export=download&confirm=t' \
  --output data/wisdom-real.rar
echo '70e76b6a5f2f83ac2c2128b021e8784cc9dc55353719ee6e0f04228d14b3efd5  data/wisdom-real.rar' | sha256sum -c -
7zz x -odata data/wisdom-real.rar

jupyter notebook EDA.ipynb
```

Для распаковки нужна версия 7-Zip с поддержкой RAR (команда `7zz`); альтернатива — `unrar x data/wisdom-real.rar data/`. После распаковки ожидается структура `data/wisdom-real/{high-res,low-res}`. Если данные находятся в другом месте, задайте переменную окружения `WISDOM_ROOT` с путём к `wisdom-real`.

В Jupyter выберите **Restart Kernel and Run All Cells**. Для запуска из терминала: `jupyter nbconvert --to notebook --execute --inplace --ExecutePreprocessor.timeout=240 EDA.ipynb`. Архив, распакованные данные и виртуальное окружение не входят в репозиторий. При добавлении файлов в Git указывайте нужные файлы явно.
