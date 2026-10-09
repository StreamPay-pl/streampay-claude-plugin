# Plugin StreamPay

Plugin łączy obsługiwanych asystentów AI z kontem twórcy StreamPay przez OAuth 2.0. Użytkownik widzi wymagane uprawnienia i sam je akceptuje. Wersja 1.0.4 opisuje 22 narzędzia MCP.

## Możliwości i ograniczenia

- Wpłaty, statystyki i ranking wspierających.
- Lista wszystkich własnych widgetów oraz wszystkich szablonów komunikatów, także nieprzypisanych do progów. Subskrypcje Kick v2 są edytowalne; pozostałe zdarzenia Twitch/Kick udostępniają tylko identyfikator, typ i wersję.
- Tworzenie celów, rankingów, odliczań oraz dodatkowych szablonów alertu v2. Alert wpłaty jest jeden na konto, ale może mieć wiele szablonów.
- Style tekstów, odstępy rankingów, pozycje elementów alertów v2, animacje, wybór głosu TTS, progi kwotowe i wygląd odliczania.
- Filtr wulgaryzmów, własna lista blokowanych fraz i ustawienia strony wpłat.
- Testowy komunikat wyłącznie na wyraźną prośbę. Może pojawić się i odtworzyć istniejący dźwięk na transmisji. Nie tworzy wpłaty, postępu celu ani audio TTS. Potwierdzenie kolejki nie oznacza wyświetlenia.

Podkreślenie tekstu ustaw przez `text_styles.<część>.underline`: `true` włącza, `false` wyłącza, pominięcie pozostawia ustawienie bez zmian. Działa niezależnie od koloru, pogrubienia i kursywy w alertach v1/v2, subskrypcjach Kick v2, rankingach v1/v2, celach i odliczaniu. Przykład: `{"text_styles":{"nickname":{"color":"#ff8800","underline":true}}}`.

Zakres zmian wyglądu zależy od typu i wersji widgetu. Cele i alerty v1 mają zamkniętą listę fontów, cele i rankingi nie obsługują dowolnych współrzędnych x/y. Zmiana progów zastępuje całą listę, dlatego asystent musi wcześniej odczytać i zachować pozostałe reguły oraz ich identyfikatory. MCP nie usuwa widgetów, nie przesyła plików, nie edytuje własnego kodu ani nie steruje uruchomionym odliczaniem. Nie inicjuje płatności ani wypłat.

## Subskrypcje Kick

- `create_widget(type=kick_subscription)` tworzy jedyny widget subskrypcji Kick z początkowym szablonem i progiem bazowym od 0 miesięcy.
- `create_kick_subscription_template` dodaje nazwany szablon do istniejącego widgetu v2, bez przypisania do progów.
- `update_kick_subscription` zmienia nazwę, style nicku i miesięcy, teksty własne, układ, animacje i głos TTS. Patryk ma ID 0.
- `list_sounds` zwraca stronicowane własne dźwięki galerii: ID, nazwę i nazwę pliku, bez URL i przesyłania plików. Niejednoznaczny wybór wymaga doprecyzowania.

Opcjonalne `thresholds` i `sound_thresholds` zastępują osobne całe listy widgetu. Zachowaj pozostałe reguły, ich ID i bazowy próg szablonu od 0 miesięcy. `months_from` przyjmuje liczby z maksymalnie dwoma miejscami po przecinku; wygrywa najwyższy próg nieprzekraczający liczby miesięcy, bez warunków exact i losowania.

Przykładowa konfiguracja: czerwony nick, żółte miesiące, oba pogrubione, głos Patryk, animacje BACK_IN_DOWN / BOUNCE / BACK_OUT_RIGHT i własny plik Fanfary-Sukcesu.mp3 od 3 miesięcy. Zapis konfiguracji nie tworzy subskrypcji, płatności ani audio TTS. Test Kick nie jest dostępny.

## Zawartość

- `plugin.json` i `mcp.json` zawierają przenośny pakiet Agent Plugins używany przez OpenAI.
- `.claude-plugin/plugin.json` i `.mcp.json` zawierają pakiet dla Claude Code.
- `skills/managing-streampay/SKILL.md` zawiera wspólne instrukcje dla asystenta.

## Połączenie

Plugin łączy się z `https://api.streampay.pl/mcp`. Repozytorium nie zawiera klucza API, hasła ani danych konta recenzenta. Logowanie odbywa się w przeglądarce przez StreamPay OAuth.

Pełna instrukcja znajduje się w [poradniku StreamPay MCP](https://wiki.streampay.pl/pl/poradniki/jak-polaczyc-asystenta-ai-ze-streampay-przez-mcp).

## Test w Claude Code

Uruchom Claude Code w katalogu pakietu:

```bash
claude --plugin-dir .
```

Połącz serwer MCP `streampay`, a następnie użyj jednego z przykładowych poleceń zapisanych w skillu.

## Źródło i budowanie

Edytowalne źródło pakietu znajduje się w `plugins/streampay/` głównego repozytorium aplikacji. Publiczne repozytorium `StreamPay-pl/streampay-claude-plugin` i folder zgłoszeniowy są kopiami dystrybucyjnymi. W głównym repozytorium uruchom `npm run plugin:validate`, a następnie `npm run plugin:build`. ZIP powstaje z plików śledzonych w Git, bez danych konta recenzenta.

## Informacja dla recenzentów

Przed zgłoszeniem wersji 1.0.4 wdroż backend z migracją podkreślenia i zbuduj frontend widgetów. Potwierdź 22 narzędzia oraz boolean `underline` w schematach stylów przez autoryzowane `tools/list`. Wgranie pakietu nie wdraża backendu.

Reviewer credentials are intentionally excluded from this public package. StreamPay provides the dedicated review account and sign-in instructions only through the secure Anthropic or OpenAI submission form. The retained demo recording shows the earlier feature set; new tools are covered by the updated review scenarios.
