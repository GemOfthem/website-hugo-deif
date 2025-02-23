---
title: Ежемесячный отчёт о разработке Empires UE4, Май 2021
author: RoyAwesome
date: "2021-06-12"
post_type: UE4
---

## Unreal Engine 5

###### Roy Awesome

Unreal Engine 5 вышел в этом месяце, сильно удивив всех.  С первого взгляда видно, что это тот монументальный скачок вперед, который и обещала Epic, это хороший знак.  В этом месяце я потратил некоторое время на оценку новых функций в UE5 и сколько сил потребуется, чтобы перенести то, что мы уже сделали с Empires в UE4, на UE5.  Короткий ответ: не так уж много работы требовалось, и мы можем скоро портировать, если бы захотим.  Наиболее значительные изменения в UE5 были внесены в процесс рендеринга, а это часть движка, которую мы вообще не трогали.  Мы не делали никаких настроек или изменений, так что весь наш контент может быть легчайше перенесен в UE5.  Структура геймплея изменилась, но текущая структура UE4 все еще существует, т.е. мы можем просто использовать код в стиле UE4, пока не перейдем к новым модульным функциям.  

Основными функциями, которыми мы хотим воспользоваться в UE5: новая структура Modular Gameplay, новые функции World Partition и Deterministic Chaos Physics.

Modular Gameplay позволяет нам структурировать игровой контент таким образом, чтобы загружались только необходимые ресурсы, и позволяет нам менять местами огромное количество функций в зависимости от того, что нужно игре в данный момент.  Например, если мы разделим наши фракции на плагины Modular Gameplay, с БЕ в одном плагине и СФ в другом, у нас могут быть матчи БЕ vs БЕ, где контент СФ просто не загрузится вообще.  Возможно, в будущем мы сможем добавить дополнительные фракции, загружая их по надобности.  В добавок, плагины  Modular Gameplay могут быть загружены во время выполнения, что означает, что оно полностью поддерживает моды, добавляющие новые карты, фракции, транспортные средства, оружие… всё что угодно.  Вы сможете присоединиться к модифицированному серверу, загрузить моды, активировать их и погрузиться в игровой процесс, не выходя из игры.  Закончив и покинув сервер, вы выгрузите плагины Modular Gameplay, что позволит вам легко переключиться на другой сервер с различными модами.  Это в основном превращает огромные части нашей игры во встроенные моды, позволяя нам быстро обновлять вещи, экспериментировать с новыми функциями или даже моддерам просто заменять эту часть игры своим собственным кодом или контентом.  Это чрезвычайно захватывающе и мощно, и это даст Empires крайнее множество возможностей.

World Partition позволяет нам создавать много слоев на одной карте и накладывать их по мере необходимости.  У нас уже есть world streaming для реализации этой стратегии, но функции UE5 позволяют нам делать некоторые сумасшедшие вещи.  Мы можем создать слой M_Valley с целой плотиной, и слой с разрушенной плотиной и затопленной долиной.  Мы можем создать эффект перехода, чтобы, если вы нанесете слишком большой урон плотине, она треснет и рухнет, затопив карту.  Загружаются только видимые части карты (и если мы переходим между слоями, новый слой быстро прогружается), и мы можем создавать любое количество состояний, в которых может находиться карта, добавлять игровое пространство, изменять освещение и добавлять слои геймплея, как мы хотим.  Это совершенно новый способ для создания карт, и все это очень интересно.

Chaos Physics предназначен для транспортных средств и, наконец, позволит нам прогнозировать движение транспортных средств на клиенте и точно воспроизводить его для всех других клиентов. Это означает, что любая сила толкающая транспорт, создаст точно такую же симуляцию на всех клиентах, без лагов. Это чрезвычайно важно для нашего игрового процесса на транспорте, речь и про самолёты.  

Главный сдерживающий нас факт - многие плагины, на которые мы полагаемся в Unreal Engine, еще не обновлены.   Мы, скорее всего, портируемся с их обновлением.  

Хочу ещё кое-что подметить, мы можем воспользоваться двумя большими изменениями рендера, Nanite и Lumen, но позже.  Nanite целесообразный, поскольку у него относительно низкие затраты для рендера миллиардов треугольников, даже на более старом оборудовании. А Lumen в это время очень дорогая техника, которую мой компьютер пока не может запустить.  Вероятнее всего, мы используем Nanite пораньше, а Lumen станет дополнительной функцией “сделать мою игру потрясающей” для тех, у кого есть машины с чудовищными мощностями, что смогут его запустить.  


## Aim Down Sights (Прицеливание)

###### Roy Awesome

Прицеливание было моим главным проектом в прошлом месяце.  Цель состояла в том, чтобы создать систему ADS, которая не требует импорта или настройки анимации в Blender для работы, а также использования игровых данных и Inverse Kinematics для управления всем.

Это оказалось на удивление нелёгкой задачей.  Из меня плохой анимационный программист, а технология, что я использовал, имеет мало хороших ресурсов в Интернете.  Тем не менее, я преисполнился в этом понятии.  Что мы делаем, так это вычисляем, где будет находиться центр камеры по отношению к прицелу, в который мы целимся, а затем перемещаем оружие в это место.  Теперь мы направляем руки в нужные места с помощью Inverse Kinematics, учитывая новую позицию оружия, и это выглядит довольно хорошо.  У нас есть полный контроль над скорость прицеливания, и прямо сейчас это больше соответствует современным шутерам от первого лица.  

Поскольку нам нужны собственно прицелы для прицеливания, я разработал целую систему креплений/прицелов.  Всего у нас есть 3 типа прицелов: прицелы с точкой, где у вас есть только одна модель прицела для обзора (например, голографический или коллиматорный прицел); Прицелы с передней стойкой, где мы привязываем точку обзора оружия к передней стойке прицела, которая используется для мушки; и Оверлей прицелы, где мы прячем оружие и отрисовываем полноэкранный оверлей на камеру, этот тип используется в снайперских прицелах..  Это охватывает почти весь спектр вещей, что нам когда-либо понадобятся, включая ночное зрение или тепловизионные прицелы, если мы когда-либо их сделаем (это просто оверлей прицелы).  Выходит чрезвычайно гибкая система, и, хотя она не будет настраиваемой во время первого релиза, наличие этих опций приведет к созданию гораздо более интересной и динамичной игры пехоты.  

![alt_text](images/image3.png "image_tooltip")


![alt_text](images/image2.png "image_tooltip")

 <video controls>
  <source src="https://cdn.discordapp.com/attachments/698655659193008208/847371277936099388/2021-05-27_00-10-19.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video> 


## Дизайн техники

###### Mayama

С выпуском UE5 в ближайшем будущем было много чего обсудить о том, как мы подходим к моделям и что мы можем использовать или изменить, чтобы сделать их лучше и проще в реализации. Это значило, что у меня было немного “свободного времени”, чтобы заняться другими делами. Я трачу некоторое время на разработку транспортных средств для обеих фракций. Все модели, которые я показываю здесь, не закончены, им не хватает мелких деталей, таких как заклепки и доп. броня. Я не добавил подвеску, потому что пока не понятно, как мы хотим ее реализовать, и время на моделирование было бы потрачено впустую, если бы мы могли сделать это по-другому.

![alt_text](images/image5.png "image_tooltip")


Это моя интерпретация легкого танка NF, это культовый дизайн и более или менее талисман игры. Вот почему я не хотел полностью менять модель и просто обновил ее.


![alt_text](images/image6.png "image_tooltip")

Как вы можете видеть, новая версия немного выше благодаря башне. Оригинальная башня была действительно странной, и человек ну никак не мог пролезть через нее. Более высокий профиль также может исправить некоторые проблемы с балансом с ЛТ, что является бонусом.

![alt_text](images/image4.png "image_tooltip")

Новая ББМ Бреноди. Старая была просто блоком с башенкой. На мой взгляд, у них должна быть более агрессивно выглядящая техника, что будет соответствовать их теме угнетающей мировой державы в ближайшем будущем. Дизайн представляет собой смесь внешнего вида истребителя-невидимки, подумайте о F-117 и итальянском дизайне автомобиля 80-х годов (бонусные баллы тому, кто узнает у какой компании я “одалживаю” большинство своих дизайнерских идей). Такая форма также выровняла бы игровое поле между ЛТ СФ и ББМ БЕ.

![alt_text](images/image7.png "image_tooltip")

Моя интерпретация среднего танка БЕ. Должен признать, мне никогда не нравился оригинальный дизайн, потому что это просто танк Leopard II с меньшим количеством деталей (да и уменьшенный). Проблема заключалась в том, что это, помимо ЛТ СФ, самая любимая техника Source Empires, поэтому я попытался обновить дизайн, а не сделать совершенно новый. Я придал ему тот же изогнутый угловатый вид, что и новому ББМ БЕ. Он все еще выглядит немного “леопардово”, но теперь у него есть свой собственный характер.

![alt_text](images/image1.png "image_tooltip")

Две фотографии для сравнения размеров этих техник межу собой. Перспектива немного вводит в заблуждение, но ЛТ СФ немного шире, но не столько, как аналог БЕ.

![alt_text](images/image8.png "image_tooltip")

Средний танк БЕ намного больше, чем в Source Empires. Причина тому в том, что мы хотим сделать средние танки в качестве основного боевого танка а, тяжелые танки специализировать, что бы вы видели их реже на поле боя, в основном так, как в настоящее время играется Empires.

## ECS Bullets

###### RoyAwesome

В конце этого месяца я начал работать над ECS Bullets. Это большая техническая победа для нас и позволяет нам одновременно имитировать десятки тысяч пуль без задержек или проблем с производительностью. У нас он уже в основном работает, но только в контексте одного игрока. Я работаю над тем, чтобы имитировать пули по сети и общаться с клиентами, а затем я внедрю моделирование твёрдых снарядов, которые мы сможем использовать для всех орудий.  
