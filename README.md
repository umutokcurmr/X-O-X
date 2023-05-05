#Python ve Tkinter ile olusturulmus bir X-O-X oyunu.
# tic_tac_toe.py kutuphanesi 
#projeyi standart bir Python paketi ile olusturulabilir 
#Python standart kitapligiyla birlikte gelen Tkinter paketini kullanmak yeterlidir 
import tkinter as tk 
from itertools import cycle
from tkinter import font
from typing import NamedTuple
#kod parcasinda,import adini mevcut ad alanina tkinter getirmek icin once ice aktaririz . Tkinter'i kodumuzda  kullanmak sozkonusu oldugunda  su sekilde kisaltiriz.tktk
#Ardindan yazi tipi modulunu dogrudan tkinter'dan ice aktaririz
#NamedTuple'i yazarak ice aktarir.
class Player(NamedTuple):
    label: str
    color: str
#Player sinifini tanimladik. 
# .label niteligi, klasik oyuncu isaretleri olan X ve O'yu kaydeder veya depolar. 
# .color niteligi, Tkinter rengine sahip bir dize tutar.
#  Oyun tahtasindaki hedef oyuncuyu tanimlamak icin bu rengi kullanacagiz.

class Move(NamedTuple):
    row: int
    col: int
    label: str = ""

#Move sinifini tanimlayin.
#  .row ve .col nitelikleri, hareketin hedef hucresini tanimlayan koordinatlari tutacaktir.
#  .label ozniteligi, oyuncuyu tanimlayan isareti, X veya O'yu tutacaktir.
#  .label'in varsayilan olarak bos dize "" , bu ozel hareketin henuz oynanmadigi anlamina gelir.
BOARD_SIZE = 3
DEFAULT_PLAYERS = (
    Player(label="X", color="blue"),#burda x icin rengi mavi yaptik
    Player(label="O", color="red"),#burda  o icin rengi kirmizi yaptik
)
#BOARD_SIZE X-O-X tahtasinin boyutunu tutar
#tahtada uce uc hucre izgara olacak
#DEFAULT_PLAYERS iki ogeli bir player tanimlar.
#Her oge oyundaki bir oyuncuyu temsil eder.
#.labelHer oyuncunun nitelikleri .
#color uygun degerlere ayarlanir. Oyuncu X mavi olacak ve oyuncu O kirmizi olacak.
class TicTacToeOYUNU:
    def __init__(self, players=DEFAULT_PLAYERS, board_size=BOARD_SIZE):
        self._players = cycle(players)
        self.board_size = board_size
        self.current_player = next(self._players)
        self.winner_combo = []
        self._current_moves = []
        self._has_winner = False
        self._winning_combos = []
        self._setup_board()

    def _setup_board(self):
        self._current_moves = [
            [Move(row, col) for col in range(self.board_size)]
            for row in range(self.board_size)
        ]
        self._winning_combos = self._get_winning_combos()
#Oyunun durumunu her an yonetmek,Tahtadaki her hareketi takip etmek gerekiyor.
#Bunu yapmak icin, bir oyuncu hamle yaptiginda guncelleyeceginiz .
# _current_moves ozelligini kullanacagiz .
#_setup_board()icin baslangic  degerlerini hesaplayan yontem
 #Bos bir hareket, onu iceren hucrenin koordinatlarini ve ilk oyuncunun etiketi olarak
 # bos bir dizeyi saklar.._current_movesMove
 #Bu yontemin._get_winning_combos()'u cagirir ve donus degerini 
 #._winning_combos'a atar. 

#Hareketin dogrulanmasi
#Kazanan kontrol ediliyor
#Berabere kalmis bir oyun olup olmadigini kontrol etme
#Bir sonraki hamle icin oyuncuyu degistirme
    def _get_winning_combos(self):
        rows = [
            [(move.row, move.col) for move in row]
            for row in self._current_moves
        ]
        columns = [list(col) for col in zip(*rows)]
        first_diagonal = [row[i] for i, row in enumerate(rows)]
        second_diagonal = [col[j] for j, col in enumerate(reversed(columns))]
        return rows + columns + [first_diagonal, second_diagonal]
#Bu yontemin ana degeri ._current_moves ozniteligidir.
#Varsayilan olarak, bu oznitelik uc alt liste iceren bir liste tutacaktir.
#Her alt liste, izgarada bir satiri temsil eder ve icinde uc Move nesnesi bulunur.

#Her hucrenin koordinatlarini alarak ve bir koordinat alt listesi olusturarak izgaradaki satirlar uzerinde yeniler.
#Koordinatlarin her bir alt listesi kazanan bir kombinasyonu temsil eder.
#ikinci kavrayis, izgara sutunlarindaki her bir hucrenin koordinatlarini iceren alt listeler olusturur.
#ucuncu ve dorduncu kavrayislar, tahta kosegenlerindeki her hucrenin koordinatlarini elde etmek icin benzer bir yaklasim kullanir.
#Son olarak, yontem X-O-X tahtasindaki tum olasi kazanan kombinasyonlari iceren bir liste listesi dondurur.
    def is_valid_move(self, move):
        #Hareket gecerliyse True, aksi takdirde False dondurun.
        row, col = move.row, move.col
        move_was_not_played = self._current_moves[row][col].label == ""
        no_winner = not self._has_winner
        return no_winner and move_was_not_played
#.is_valid_move() yontemi, bir Move nesnesini bagimsiz degisken olarak alir. 
#giris hareketinden .row ve .col koordinatlarini alir.
#[row][col] koordinatlarindaki hareketin etiketi olarak hala bos bir dize tutup tutmadigini kontrol eder.
#Daha once hicbir oyuncu girdi hamlesini yapmamissa, bu kosul Dogru olacaktir.
#Satir 92, oyunun henuz kazanani olup olmadigini kontrol eder.

    def process_move(self, move):
        #Mevcut hamleyi isleyin ve bunun bir galibiyet olup olmadigini kontrol edin.
        #Satir 100, bir Move nesnesini bagimsiz degisken olarak alan process_move() ogesini tanimlar.
        
        row, col = move.row, move.col
        self._current_moves[row][col] = move
        for combo in self._winning_combos:
            results = set(self._current_moves[n][m].label for n, m in combo)
            is_win = (len(results) == 1) and ("" not in results)
            if is_win:
                self._has_winner = True
                self.winner_combo = combo
                break
#Satir 104, giris hareketinden .row ve .col koordinatlarini alir.
#satir 105, giris hareketini mevcut hareketler listesindeki [satir row][sutun col]'daki ogeye atar.
#Satir 106, kazanan kombinasyonlar uzerinde bir dongu baslatir.
#satir 107, mevcut kazanma kombinasyonundaki hareketlerden tum etiketleri alan bir olusturucu ifade calistirir.
#Sonuc daha sonra bir set nesnesine donusturulur.
#satir 108, mevcut hareketin bir galibiyet belirleyip belirlemedigini kontrol eden bir Boole ifadesi tanimlar. Sonuc is_win'de saklanir.
#Satir 109, is_win'in icerigini kontrol eder. Degiskenin degeri True ise,
#._has_winner True olarak ve .winner_combo gecerli kombinasyon olarak ayarlanir.
#Daha sonra dongu bozulur ve fonksiyon sona erer.
    def has_winner(self):
        #Oyunun bir kazanani varsa True, aksi takdirde False degerini dondurun.
        #satir122,126 mevcut macin kazanani olup olmadigini kontrol etmeniz gerektiginde
        #._has_winner'da saklanan Boolean degerini dondurur.
        return self._has_winner

    def is_tied(self):
       
        no_winner = not self._has_winner
        played_moves = (
            move.label for row in self._current_moves for move in row
        )
        return no_winner and all(played_moves)
#Satir 128 .is_tied() icinde, once oyunun henuz kazanani olup olmadigini kontrol ederiz.
#Ardindan, ._current_moves icindeki tum hareketlerin bos dizeden farkli bir etiketi olup olmadigini kontrol etmek icin yerlesik all() islevini kullanirsiniz.
#Durum buysa, olasi tum hucreler zaten oynanmistir. Her iki kosul da dogruysa, oyun berabere biter.
    def toggle_player(self):
       #Degistirilmis bir oyuncuyu dondur.
        self.current_player = next(self._players)

    def reset_game(self):
       #Degistirilmis bir oyuncuyu dondur.
        for row, row_content in enumerate(self._current_moves):
            for col, _ in enumerate(row_content):
                row_content[col] = Move(row, col)
        self._has_winner = False
        self.winner_combo = []


class TicTacToeBoard(tk.Tk):
    def __init__(self, game):
        super().__init__()
        self.title("X-O-X oyunu")# oyuna baslik verdik 
        self._cells = {}
        self._game = game
        self._create_menu()
        self._create_board_display()
        self._create_board_grid()

    def _create_menu(self):
        menu_bar = tk.Menu(master=self)
        self.config(menu=menu_bar)
        file_menu = tk.Menu(master=menu_bar)
        file_menu.add_command(label="TEKRAR OYNA", command=self.reset_board)
        file_menu.add_separator()
        file_menu.add_command(label="KAPAT", command=quit)
        menu_bar.add_cascade(label="DOSYA", menu=file_menu)

    def _create_board_display(self):
        display_frame = tk.Frame(master=self)
        display_frame.pack(fill=tk.X)
        self.display = tk.Label(
            master=display_frame,
            text="HAZIR MISIN?",
            font=font.Font(size=28, weight="bold"),
        )
        self.display.pack()

    def _create_board_grid(self):
        grid_frame = tk.Frame(master=self)
        grid_frame.pack()
        for row in range(self._game.board_size):
            self.rowconfigure(row, weight=1, minsize=50)
            self.columnconfigure(row, weight=1, minsize=75)
            for col in range(self._game.board_size):
#Satir 151,186 arasi,once TicTacToeBoard baslaticiya bir oyun argumani ekleriz.
#Ardindan bu bagimsiz degiskeni, oyun tahtasindan oyun mantigina tam erisim saglayacak olan .
#_game adli bir ornek ozniteligine atariz.
#Tahta boyutunu ayarlamak icin sihirli bir sayi kullanmak yerine .
#_game.board_size kullanmaktir. Bu guncelleme ayni zamanda farkli pano boyutlari kullanmaniza da olanak tanir.
#4'e 4'luk bir pano izgarasi olusturabiliriz.


                button = tk.Button(
                    master=grid_frame,
                    text="",
                    font=font.Font(size=36, weight="bold"),
                    fg="black",
                    width=3,
                    height=2,
                    highlightbackground="lightblue",
                )
                self._cells[button] = (row, col)
                button.bind("<ButtonPress-1>", self.play)
                button.grid(row=row, column=col, padx=5, pady=5, sticky="nsew")

    def play(self, event):
        #Bir oyuncunun hamlesini yonet.
        clicked_btn = event.widget
        row, col = self._cells[clicked_btn]
        move = Move(row, col, self._game.current_player.label)
        if self._game.is_valid_move(move):
            self._update_button(clicked_btn)
            self._game.process_move(move)
            if self._game.is_tied():
                self._update_display(msg="Berabere Oyun!", color="red")#oyun durumunu burdan ayarliyoz berabere 
            elif self._game.has_winner():
                self._highlight_cells()
                msg = f' "{self._game.current_player.label}" KAZANDIN!'#oyunu kazandi
                color = self._game.current_player.color
                self._update_display(msg, color)
            else:
                self._game.toggle_player()
                msg = f"{self._game.current_player.label}'SIRA SENDE"#oyuna baslama oyuna x baslar 
                self._update_display(msg)
#Satir  208, Bir Tkinter olay nesnesini arguman olarak alan play()'i tanimlar.
#Satir  210, gecerli olayi tetikleyen pencere ogesini alir. Bu widget, pano izgarasindaki dugmelerden biri olacaktir.
#Satir  211, dugmenin koordinatlarini satir ve sutun olmak uzere iki yerel degiskene ayirir.
#Satir  212, satir, sutun ve gecerli oyuncunun .label ozniteligini arguman olarak kullanarak yeni bir Move nesnesi olusturur.
#Satir  213, oyuncunun hamlesinin gecerli olup olmadigini kontrol eden kosullu bir ifade baslatir.
#Tasima gecerliyse, if kod blogu calisir. Aksi takdirde baska bir islem yapilmaz.
#Satir  214, ._update_button() yontemini cagirarak tiklanan dugmeyi gunceller.
#Satir  215, ._game nesnesinde gecerli hareketi arguman olarak kullanarak .process_move() ogesini cagirir.
#Satir  216, oyunun berabere olup olmadigini kontrol eder. Durum buysa, oyun ekrani buna gore guncellenir.
#Satir  218, mevcut oyuncunun oyunu kazanip kazanmadigini kontrol eder.
#Ardindan 219. satir, kazanan hucreleri vurgular ve 220'dan 222'e kadar olan satirlar, kazanani kabul etmek icin oyun ekranini gunceller.
#Satir  223, oyun berabere degilse ve kazanan yoksa calisir. Bu durumda, 224'ten 226'e kadar olan satirlar,
#Oyuncuyu bir sonraki hamle icin degistirir ve bir sonraki oynayacak oyuncuyu belirtmek icin ekrani gunceller.

    def _update_button(self, clicked_btn):
        clicked_btn.config(text=self._game.current_player.label)
        clicked_btn.config(fg=self._game.current_player.color)
#Bu yontemde, oyun ekraninin metnini ve rengini degistirmek icin .config() kullanmak yerine,
#sozluk tarzi bir alt simge gosterimi kullanabiliriz.
#Bu tur gosterimi kullanmak, Tkinter'in bir parcacigin ozniteliklerine erismek icin sagladigi baska bir secenektir.
#Son olarak, belirli bir oyuncu kazanan bir hamle yaptiginda kazanan hucreleri vurgulamak icin yardimci bir yonteme ihtiyacimiz var.
    def _update_display(self, msg, color="black"):
        self.display["text"] = msg
        self.display["fg"] = color

    def _highlight_cells(self):
        for button, coordinates in self._cells.items():
            if coordinates in self._game.winner_combo:
                button.config(highlightbackground="red")
#._highlight_cells() icindeki dongu, ._cells sozlugundeki ogeler uzerinde yinelenir.
#Bu sozluk, dugmeleri pano izgarasindaki satir ve sutun kordinatlarina esler.
#Mevcut kordinatlar kazanan bir hucre kombinasyonundaysa, dugmenin arka plan rengi tahtada hucre kombinasyonunu vurgulayarak "kirmizi" olarak ayarlanir.
#Bu son yardimci yontem uygulandiginda, X-O-X oyununuz ilk mac icin hazir.
    def reset_board(self):
        #Tekrar oynamak icin oyun tahtasini sifirlayin
        self._game.reset_game()
        self._update_display(msg="Ready?")
        for button in self._cells.keys():
            button.config(highlightbackground="lightblue")
            button.config(text="")
            button.config(fg="black")


def main():
    #Oyunun tahtasini olustur ve ana dongusunu calistir.
    game = TicTacToeOYUNU()
    board = TicTacToeBoard(game)
    board.mainloop()
#Bu kodda, vurgulanan ilk satir, oyun mantigini yonetmek icin kullandik bir X-O-X ornegi olusturur.
#Vurgulanan ikinci satir, yeni ornegi, oyun mantigini oyun tahtasina ekleyen TicTacToeBoard sinifi olusturucusuna iletir.

if __name__ == "__main__":
    main()
