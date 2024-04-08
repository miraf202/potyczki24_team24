# Witamy w finale! TEAM24
Potyczki Młodych Adminów 2024 - TEAM24
ODPOWIEDZI ZNAJDUJĄ SIĘ OBOK PYTAŃ!!!!

## Wstęp
Tegoroczne zadania są pomyślane jako dzień z pracy administratora w korporacji. Jest trochę codziennych, podstawowych czynności, trochę pomagamy mniej doświadczonym kolegom, jest znaczący nacisk na elementy związane z bezpieczeństwem, są też wyzwania i awarie, z którymi należy jak najszybciej się uporać bo kosztują naszą organizację pieniądze. Czasem jedna litera jest różnicą między pracą dobrze zrobioną, a niewytłumaczalnym (albo niewybaczalnym) błędem. Jak w prawdziwym życiu, zadań jest więcej niż czasu i warto odpowiednio priorytetyzować.

Powodzenia!

---

### "Zadanie 0"
Najlepiej napisany program i najlepiej wdrożony system jest tykającą bombą bez odpowiedniej dokumentacji. Nikt nie lubi jej pisać, ale jest kluczowa dla łatwości późniejszego utrzymywania i efektywnej współpracy - a także do sprawdzania zadań! Dlatego udokumentuj wszystkie zadania, co najmniej oznaczając te które zostały wykonane, bo **tylko te zostaną sprawdzone**. Niektóre zadania wymagają pisemnej odpowiedzi, umieść je też w dokumentacji.

Opisz kroki wykonane w celu realizacji zadania, szczególnie lokalizacje zasobów, użyte opcje i komendy - nie musisz tego robić bardzo dokładnie, ale w razie wątpliwości będą one działać na twoją korzyść. Na przykład jeśli zadanie nie zostało do końca wykonane, ale znacząca część kroków jest opisana poprawnie, zaliczymy za to częściowe punkty. Albo jeśli zadanie zostało wykonane, ale nie w sposób jaki był spodziewany, to opis będzie kluczem do uzyskania za nie punktów. To, co nie jest opisane, a nie jest oczywiste z interfejsu, będzie rozstrzygane na twoją niekorzyść.

### Zadanie 1 ZROBIONE CZĘŚCIOWO
Na klastrze "potyczki" utwórz projekt "web-server" a w nim namespace "nginx". W tym namespace uruchom kontener “nginx” w najnowszej wersji. **5pkt** **ZROBIONE** (Create Project-Create Namespace-)
- Utwórz usługę dzieki której można się odwołać do naszego nginx z całego klastra **5pkt**
- Chcemy zapewnić wysoką dostępność tej usługi - upewnij się, że cały czas będą działały co najmniej trzy repliki naszego kontenera **2pkt** **ZROBIONE (Ustawienie scale na 4)**
- Zapewnij dostępność usługi na internet. Nie masz czasu czekać aż administratorzy sieci udostępnią ci firmowy DNS, a potrzebujesz szybko przetestować dostępność, więc wymyśl jak zapewnić rozwiązywalny url wskazujący na IP hosta, na którym jest twój klaster "potyczki". **25pkt** (pełnym rozwiązaniem jest podanie adresu typu nginx.xxxx.xxxx.xx rozwiązywalnego przy pomocy DNS z internetu, pod którym zgłosi się działająca usługa);

### Zadanie 2 NIE ZROBIONE
 - Sprawdź czy host klastra "potyczki" spełnia wymagania (prerequisites) dla aplikacji Longhorn; doinstaluj ewentualne braki. **5pkt**
 - Z katalogu aplikacji wbudowanych Ranchera zainstaluj Longhorn w najnowszej stabilnej wersji na klastrze "potyczki", ustawiając w konfiguracji instalacyjnej 1 replikę i domyślny StorageClass. **5pkt**
 - Potwierdź status wszystkich podów Longhorna **7pkt**

### Zadanie 3 ZROBIONE
Dodaj nowe repozytorium do katalogu aplikacji Ranchera. URL repo: https://rancher.github.io/rodeo **5pkt**

**(W rancher Apps-Repositories, następnie create)**

Zainstaluj aplikację Tetris z nowo dodanego repo. **5 pkt**

**(Apps-Charts-tetris, zainstalowany do nginx)**

### Zadanie 4
Z katalogu aplikacji zainstaluj NeuVector w najnowszej stabilnej wersji. **10pkt**

**(Apps-Charts-NeuVector, zainstalowany do projektu web-server)**

Włącz funkcję Auto-scan **5 pkt**

**(Panel NeuVector-Assets-Platforms-Auto Scan) Opcja dostępna w prawym górnym rogu**

### Zadanie 5 NIE ZROBIONE
Nasi deweloperzy chcą korzystać z publicznie dostępnych obrazów, ale mogą one zawierać groźne podatności. Dlatego chcemy najperw przeskanować rejestr, zanim zaczniemy z niego korzystać. Użyj NeuVector, żeby przeskanować sekcję nvbeta/* w rejestrze https://registry.hub.docker.com ; jako rozwiązanie podaj nazwę image z największą ilością podatności. **7pkt**

### Zadanie 6 ZROBIONE
Po dyskusjach z działem biznesowym doszliśmy do wniosku, że część usług możemy wygasić na noc, żeby zminimalizować koszty. Niestety przeskalowanie do 0 (czyli de facto usunięcie usunięcie poda) powoduje automatyczne usunięcie PersistentVolueClaim, co automatycznie usuwa też przypisany PersistentVolume. A chcemy zachować PersistentVolume i automatycznie podłączyć się do niego z powrotem przy uruchomieniu usługi rano. Jeden z tych deploymentów możliwych do wygaszania jest opisany w "nie-usuwaj.yaml". Zaproponuj rozwiązanie, które zapewni, że po przeskalowaniu do 0 i ponownym wznowieniu opisany deployment "znajdzie" PersistentVolume używany przez poprzednie repliki i się do niego podłączy.

**30 pkt** za odpowiedź teoretyczną wraz z odpowiednim przykładowym kodem yaml.

Aby osiągnąć to za pomocą YAML, musimy ustawić odpowiednią etykietę dla PersistentVolumeClaim, która pozwoli na automatyczne powiązanie z istniejącym PersistentVolume. Możemy użyć etykiety, która identyfikuje PersistentVolume jako skojarzony z określonym deploymentem lub usługą.
Przykładowy kod YAML dla PersistentVolumeClaim :
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: team24
  labels:
    app: aplikacja
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
```
30pkt za odpowiednie zmodyfikowanie i zdeployowanie "nie-usuwaj.yaml" demonstrując działanie teoretycznego rozwiązania na klastrze "potyczki"; 
```yaml
---
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: ubuntu-pvc
  labels:
    app: ubuntu   # Dodana etykieta identyfikująca deployment
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
---
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: ubuntu   # Etykieta identyfikująca deployment
  name: ubuntu
spec:
  replicas: 1
  selector:
    matchLabels:
      app: ubuntu
  template:
    metadata:
      labels:
        app: ubuntu   # Etykieta identyfikująca pod w ramach deploymentu
    spec:
      volumes:
        - name: test-vol
          persistentVolumeClaim:
            claimName: ubuntu-pvc
      containers:
        - name: ubuntu
          image: busybox:latest
          command:
          - sleep
          - "86400"
          volumeMounts:
            - mountPath: /shared_files
              name: test-vol

```
+5 pkt za użycie provisionera storage z Zadania 2.

### Zadanie 7 ZROBIONE
Twój niezbyt rozgarnięty kolega z pracy, Adrian, prosi cię o poradę: w klastrze mam pewien resource, ale nie wiem jak znaleźć yaml tego zasobu? Jak go podejrzeć?
**5pkt**, +**7pkt** za dodatkową metodę

**Pierwszy sposób**
 - Zalogowanie się do klastra w kubectl - “kubectl config use-context nazwa-klastra”
 - Pobranie yaml zasobu - “kubectl get nazwa-zasobu nazwa-zasobu -o yaml --export > zasob.yaml”
**Drugi Sposób**
 - Znalezienie swojego zasobu w rancherze, pod “trzema kropkami”, kryje się opcja “Download YAML”


### Zadanie ZROBIONE
Adrian uruchomił aplikację składającą się z front-endu i bazy danych MySQL. Widzisz, że jego kontener MySQL jest uruchomiony na najprostszych domyślnych ustawieniach. Czy jest to zalecany sposób? Uzasadnij w kilku zdaniach (min. 20 słów dla pełnej punktacji).
**5pkt**, +**5pkt** jeśli uzasadnienie zawiera za i przeciw oraz sugestię poprawy

**Uruchamianie kontenera MySQL na najprostszych domyślnych ustawieniach jest dosyć ryzykowne, ponieważ nie jest to zbyt bezpieczne.
Za:
Uruchomienie MySQL na defaultowych jest szybkie i łatwe do wykonania.
Przeciw:
Default MySQL niestety nie uwzględnia kwestii bezpieczeństwa. M.in zabezpieczenia dostępu, przez co są luki w bezpieczeństwie i naraża system na ataki.
Sugestia poprawy:
Zaleca się stosowanie silnych haseł dostępu, ograniczenia dostępu zewnętrznego, regularne tworzenie kopii zapasowych danych oraz optymalizację ustawień wydajności.**

### Zadanie 8 ZROBIONE
Wytłumacz Adrianowi w kilku prostych zdaniach czym jest resource o nazwie Gateway w Kubernetes? (min. 20 słów dla pełnej punktacji)
**7pkt**

**Gateway w Kubernetes to rodzaj zasobu opisujący instancję infrastruktury obsługi ruchu. Definiuje punkt końcowy sieci, który może być używany do przetwarzania ruchu dla backendów, takich jak usługa. Może reprezentować moduł równoważenia obciążenia w chmurze lub serwer proxy w klastrze skonfigurowany do akceptowania ruchu HTTP.**

### Zadanie 9 ZROBIONE
Adrian jest bardzo skonfundowany dlaczego są dwa różne resource w Kubernetes, które "robią to samo" czyli zarządzają zestawem identycznych podów: Deployment i ReplicaSet. Wyjaśnij mu na czym polega różnica między tymi resource'ami. (min. 20 słów dla pełnej punktacji)
**7pkt**

**Gateway w Kubernetes to rodzaj zasobu opisujący instancję infrastruktury obsługi ruchu. Definiuje punkt końcowy sieci, który może być używany do przetwarzania ruchu dla backendów, takich jak usługa. Może reprezentować moduł równoważenia obciążenia w chmurze lub serwer proxy w klastrze skonfigurowany do akceptowania ruchu HTTP.**

### Zadanie 10 NIE ZROBIONE
Dokonaj aktualizacji klastra "potyczki" to nowszej wersji Kubernetes tak, żeby zminimalizować jej wpływ na dostępność uruchomionych workloadów. **10pkt**

### Zadanie 11 CZĘŚCIOWO ZROBIONE
Firma zatrudniła właśnie dwóch nowych pracowników, jako administrator środowiska Kubernetes twoim zadaniem jest utworzyć dla nich konta użytkowników o nazwach w formacie imie.nazwisko i poprawnie przypisać im uprawnienia:
- Nowym dyrektorem IT został Muhammed Yassuff i nalega, żeby mieć podgląd na działanie całego środowiska (uprawnienia get, list, watch).
- Przyjęliśmy także świeżego praktykanta, któremu na razie powierzyliśmy tylko utrzymanie (tj. pełna kontrola) projektu "web-server". Nazywa się on Muhhamad Yussuff.

**5 pkt** za utworzenie dwóch lokalnych użytkowników, po **7 pkt** za poprawne przypisanie uprawnień do każdego z nich (**+5** dodatkowych punktów za rozwiązanie bez żadnej pomyłki i nie nadanie dyrektorowi prawa do podglądu sekretów)

**Użytkownicy utworzeni, w “Users & Authentication”, uprawnienia poprawne tylko u dyrektora**

### Zadanie 12 ZROBIONE
Jeden z workloadów na klastrze "potyczki", Deployment o nazwie "mysql", nie działa poprawnie. Deweloperzy napisali yaml, ale winią Adriana bo on go zdeployował na klastrze i na pewno coś popsuł bo yaml przecież był ok. Znajdź przyczynę błędu i napraw go. **30 pkt**
**Problemem była zbyt mała ilość pamięci dana do deploymentu. Działa poprawnie zwiększona pamięci z 64Mi do 512Mi**

### Zadanie 13 NIE ZROBIONE
Nasz workload "nginx" z projektu "web-server" (Zadanie 1) jest prawdopodobnie atakowany z internetu! Użyj NeuVector, żeby zwizualizować wszystkie połączenia sieciowe w klastrze i zapisz zrzut ekranu do dokumentacji (**5 pkt**), oraz przechwyć i zapisz pakiety z ruchu przychodzącego do "nginx" (**10 pkt**). Jeśli Zadanie 1 jest niewykonane, możesz przechwycić pakiety innego poda (udokumentuj który). Możesz sztucznie wygenerować zapytania, żeby mieć co przechwycić.

+**7 pkt** za opis na czym polega analiza pakietów i podanie przykładowego narzędzia do takiej analizy (min. 20 słów dla pełnej punktacji)

### Zadanie 14 ZROBIONE
Adrian próbuje zdeployować nowy workload i chyba tym razem rzeczywiście coś zepsuł bo za nic nie chce się to uruchomić. Napraw i uruchom adrian-nginx.yaml w nowym namespace o nazwie "adrian". **40 pkt**
**Brak ścieżki /somepath. Rozwiązanie: wyłączenie liveness check.**

### Zadanie 15 NIE ZROBIONE
Przy pomocy NeuVector utwórz regułę blokującą połączenia wychodzące z nginx (Zadanie 3) na zewnątrz klastra i przełącz w tryb aktywnej ochrony (Protect). (**10 pkt**) Wyeksportuj regułę jako CRD w trybie Protect i załącz do dokumentacji (**5 pkt**). Potwierdź działanie reguły logując się do shella poda nginx i wykonując polecenie curl suse.com  (**7 pkt**). Zablokuj również samo polecenie curl w tym podzie i potwierdź działanie reguły logując się do shella. (**10 pkt**). Dopuszczalne potwierdzenia to zrzuty ekranu lub skopiowane w całości komunikaty shella wraz z poleceniem, które je wyzwoliło - dołącz do dokumentacji.

### Zadanie 16 NIE ZROBIONE
Jedna z naszych Service nie może się połączyć ze wskazanym Deployment'em. Uruchom "serwis.yaml" w nowym namespace "serwis" i napraw przyczynę problemu. Rozwiązaniem jest Service wskazujący poprawnie na Pod'a nginx zdeployowanego przez "serwis.yaml". **35 pkt**

### Zadanie 17 ZROBIONE
Kolejna instancja mysql sprawia problemy, Adrian od trzech dni przez nią nie śpi bo trzyma klaster i przecież wszystko sprawdził. Uruchom baza.yaml w nowym namespace "baza" i doprowadź do poprawnego załadowania się bazy danych. **25 pkt**
**Stworzenie nowego namespace o nazwie baza. Zimportowanie pliku**
**Błąd w pliku baza.yaml: Zmiana MYSQL_ROT_PASSWORD na MYSQL_ROOT_PASSWORD**

### Zadanie 18 ZROBIONE
Adrian chciał uruchomić aplikację "hello-world", ale nawet to mu nie działa. Znajdź przyczynę problemu i uruchom hello-world. **20 pkt**
**Zmiana container-image na rancher/hello-world, problemem była wersja arm64, nie zgodna z architekturą komputera.**
