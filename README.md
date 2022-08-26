# game_sodoku
from kivy.app import App
from kivy.uix.button import Button
from kivy.config import Config
from kivy.uix.boxlayout import BoxLayout
from kivy.uix.anchorlayout import AnchorLayout
from kivy.uix.textinput import TextInput
from kivy.uix.label import Label
import time
import random
import time


def swap_colum(m, n=random.randint(20, 100)):
    for _ in range(n):
        rand_colum = -random.randint(0, 2) * 3
        rand_s_colum = random.randint(1, 2)
        for j in range(9):
            m[j][rand_colum], m[j][rand_colum + rand_s_colum] = m[j][rand_colum + rand_s_colum], m[j][rand_colum]

    return m


def swap_rows(matrix, n=random.randint(20, 100)):
    for _ in range(n):
        rand_rows = -random.randint(0, 2) * 3
        rand_s_rows = random.randint(1, 2)
        for j in range(9):
            matrix[rand_rows][j], matrix[rand_rows + rand_s_rows][j] = matrix[rand_rows + rand_s_rows][j], \
                                                                       matrix[rand_rows][j]

    return matrix


def gen_matrix(n):
    matrix = []
    for i in range(n):
        matrix.append([1])
        for j in range(2, n + 1):
            matrix[i].extend([j])

    matrix[6] = matrix[6][2:len(matrix)] + matrix[6][:2]
    matrix[3] = matrix[3][1:len(matrix)] + matrix[3][:1]

    for i in 0, 3, 6:
        for j in range(1, 3):
            if j == 1:
                matrix[i + j] = matrix[i][3:len(matrix)] + matrix[i][:3]
            else:
                matrix[i + j] = matrix[i][6:len(matrix)] + matrix[i][:6]

    return matrix


def print_matrix(matrix):
    for i in range(len(matrix)):
        print(*matrix[i])


def random_clien_sudoku(matrix, n):
    clien_matrix = []
    for i in range(9):
        if random.randint(0, 10) < n:
            clien_matrix.append([matrix[i][0]])
        else:
            clien_matrix.append([0])
        for j in range(1, 9):
            if random.randint(0, 10) < n:
                clien_matrix[i].extend([matrix[i][j]])
            else:
                clien_matrix[i].extend([0])
    return clien_matrix


def main_loop():
    matrix = gen_matrix(9)

    for _ in range(random.randint(10, 50)):
        rand = random.randint(1, 2)
        if rand % 2 == 0:
            matrix = swap_rows(matrix)
        else:
            matrix = swap_colum(matrix)
    return matrix


class MainApp(App):

    def on_press_button_int(self, instance):
        text_str = instance.text
        if instance.background_color == [.26, .51, .71, 1]:
            pass
        else:
            instance.text = str(int(text_str[0]) + 1) + text_str[1:len(text_str)] if int(text_str[0]) < 9 else str(
                1) + text_str[1:len(text_str)]
        print(instance.text)
        client_matrix[int(text_str[9])][int(text_str[10])] = int(instance.text[0])


    def on_press_button(self, instance):
        if instance.text == "UPDATE":
            matrix_new = main_loop()
            # print_matrix(matrix)
            client_matrix_new = random_clien_sudoku(matrix_new, complexity)
            for i in range(9):
                for j in range(9):
                    matrix[i][j] = matrix_new[i][j]
                    client_matrix[i][j] = client_matrix_new[i][j]
            main_window.remove_widget(box_sudoku)
            main_window.remove_widget(check)
            main_window.remove_widget(label_loyout_end)
            new_sudoku = main_sudoku()
            main_window.add_widget(new_sudoku)
            main_window.add_widget(check)
            main_window.add_widget(label_loyout_end)


    def check_sudoku(self, instance):
        test = False
        for i in range(9):
            for j in range(9):
                if client_matrix == matrix:
                    test = True
                    break
        if test:
            instance.text = "WIN"
        else:
            instance.text = "LOSE"


    def build(self):

        global main_window
        main_window = BoxLayout(orientation="vertical",
                                pos_hint={'center_x': .5, 'center_y': .5},
                                spacing=30,
                                padding=[0]
                                )
        # label
        global label_loyout
        label_loyout = Label(size_hint=(1, .1),
                             text='[b]' + "SUDOKU" + '[/b]',
                             markup=True,
                             color=[0, 0, 1, 1],
                             font_size='40sp',
                             )

        # label_end
        global label_loyout_end
        label_loyout_end = Label(size_hint=(1, .1))

        # start
        global start
        start = Button(text='UPDATE',
                       size_hint=(.1, .05),
                       pos_hint={'center_x': .5, 'center_y': .5},
                       on_press=self.on_press_button,
                       background_color=[255, 255, 255],
                       background_normal="",
                       color=[0, 0, 1, 1], )

        # check
        global check
        check = Button(text='CHECK',
                       size_hint=(.1, .05),
                       pos_hint={'center_x': .5, 'center_y': .5},
                       on_press=self.check_sudoku,
                       background_color=[255, 255, 255],
                       background_normal="",
                       color=[0, 0, 1, 1], )

        # Sudoku block
        global main_sudoku
        def main_sudoku():
            float_test = True
            global box_sudoku
            box_sudoku = BoxLayout(orientation="vertical",
                                   size_hint=(.5, .4),
                                   pos_hint={'center_x': .5, 'center_y': .5}, )
            for row_block in 0, 3, 6:
                # global row_loyout
                row_loyout = BoxLayout()
                for col_block in 0, 3, 6:
                    # global col_loyout
                    col_loyout = BoxLayout(spacing=0, padding=[3.5])
                    for row_col_block in 0, 1, 2:
                        # global block
                        block = BoxLayout(orientation="vertical", spacing=-0.5, padding=[-0.5])
                        for j in range(3):
                            fgc = [.83,.94,1,1] if float_test is False else [255, 255, 255, 1]
                            fgc = fgc if client_matrix[row_block + j][row_col_block + col_block] == 0 else [.26, .51, .71, 1]
                            float_test = not float_test
                            graf = Button(
                                text=str(client_matrix[row_block + j][
                                             row_col_block + col_block]) + '[size=0]' + f"{row_block + j}{row_col_block + col_block}" + '[/size]',
                                markup=True,
                                font_size='20sp',
                                pos_hint={"center_x": 0.5, "center_y": 0.5},
                                background_color=fgc,
                                background_normal="",
                                color=[0, 0, 1, 1],
                                on_press=self.on_press_button_int,
                            )
                            block.add_widget(graf)
                        col_loyout.add_widget(block)
                    row_loyout.add_widget(col_loyout)
                box_sudoku.add_widget(row_loyout)
            return box_sudoku

        # First_gen_matrix
        global matrix
        matrix = main_loop()
        global client_matrix
        client_matrix = random_clien_sudoku(matrix, complexity)
        box_sudoku = main_sudoku()
        # completing the field
        main_window.add_widget(label_loyout)
        main_window.add_widget(start)
        main_window.add_widget(box_sudoku)
        main_window.add_widget(check)
        main_window.add_widget(label_loyout_end)

        return main_window


if __name__ == '__main__':
    complexity = 10 - int(input("Difficulty from 0 to 10: "))
    assert -1 < complexity < 11 and type(complexity) is int
    Config.set("graphics", "resizable", "0")
    Config.set("graphics", "widht", "600")
    Config.set("graphics", "height", "800")
    app = MainApp()
    app.run()
