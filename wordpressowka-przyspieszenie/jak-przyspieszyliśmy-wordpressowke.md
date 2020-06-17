# Jak przyspieszyliśmy WordPressówkę?

Jednym z celów jaki sobie postawiłem pracując nad nową wersją WordPressówki była jej optymalizacja. Mimo, że poprzednia wersja była minimalistyczna i lekka to i tak postanowiłem, że będzie lepiej i szybciej.

## Zacznijmy od porównania

Poprzednia wersja strony ważyła 300 KB, wczytywała się 1.7 sekund oraz generowała 20 zapytań.

Nowa wersja waży 159 KB, wczytuje się 1.4 sekundy i generuje 13 requestów.

Oba pomiary wykonałem za pomocą Web Page Speed z serwera londyńskiego. Porównałem w obu przypadkach strony które nie miały obrazków w treści.

## Najpierw była dieta

Pierwszym krokiem, żeby tak przyspieszyć stronę było pozbycie się wszystkiego co zbędne. Na pierwszy ogień poszły więc komentarze (nie były popularne, więc stwierdziliśmy, że nie ma sensu ich trzymać).

Następnie zrezygnowaliśmy z jQuery. W sumie była to pierwsza moja którą stworzyłem bez użycia tej biblioteki. Poza korzyściami związanymi z wydajnością okazało się, że pisanie w czystym JS zrobiło się o wiele bardziej znośne.

Ograniczyliśmy też pluginy do absolutnego minimum - Advanced Custom Fields, Yoast oraz Algolia.

## Zmiany stron bez przeładowania

Postanowiliśmy dodać też skrypt barba.js - pozwala on przeładowywać tylko fragment strony. Dzięki temu skrypty i style są ładowane tylko raz. Działa to naprawdę bardzo fajnie i jest naprawdę proste w implementacji.

## Style, skrypty i obrazki

Ważne też było podzielenie CSS na dwie części - na tą część, która jest niezbędna do wyświetlenia strony (tzw. critical CSS) oraz resztę, która jest wczytana dopiero na końcu. Tutaj głównie pomaga mi usługa criticalcss.com.

Wszystkie skrypty są wczytywane asynchronicznie, a większość obrazków jest wczytywana dopiero kiedy jest potrzebna - wszystko za pomocą biblioteki lazysizes.

Jak już jesteśmy przy obrazkach - za pomocą picture wczytywane są obrazki odpowiedniego rozmiaru w zależności od rozdzielczości ekranu. Nie mogło też zabraknąć użycia webp. Swoją drogą bardzo zalecam taką strategię - dzięki niej możemy bardzo odchudzić stronę.

## Jesteśmy statyczni

Wąskim gardłem wielu stron jest właśnie baza danych. Dlatego z niej też zrezygnowaliśmy. Cała strona jest generowana za pomocą wp2static oraz kilku skryptów wywoływanych po stronie serwera.

W efekcie pozbyliśmy się głównego problemu. Tworzenie strony statycznej wiąże się z pewnymi problemami - stąd decyzja żeby zamiast wordpressowej wyszukiwarki musieliśmy użyć Algolii. Było to jednak jedyne ustępstwo na jakie musieliśmy pójść.

## Deployment

Całość odbywa się za pomocą Buddy.works i sprowadza się do 3 pipelinów:

- pierwszy odpowiada za update strony - czyli typowy update pluginów oraz kodu
- drugi odpowiada za przygotowanie wszystkiego po stronie wp2static. Jako, że plugin można uruchomić za pomocą wp-cli, całość ogranicza się do wstępnej konfiguracji i uruchomienia wp2static generate oraz wp2static deploy. Kiedy to nastąpi kopiowane są jeszcze brakujące obrazki i następuje podmiana katalogów.
- trzeci odpowiada za aktualizację indeksów na Algolii

Przyznam, że było to coś czego się obawiałem - bałem się, że deployment będzie zajmował ponad 5 minut. Jakie było moje zaskoczenie kiedy okazało się, że cały proces trwa minutę i kilka sekund. 

## Efekty

Jak już wspomniałem na początku strona jest lżejsza o około 50% i wczytuje się o 80% krócej. W połączeniu z płynnym przechodzeniem między stronami, muszę przyznać, że efekt jest bardzo zadowalający. Na tyle wręcz, że chwilowo skończyły mi się pomysły na to co poprawiać.
