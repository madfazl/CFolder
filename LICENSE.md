import os
from PySide6.QtWidgets import (QApplication, QWidget, QVBoxLayout, QHBoxLayout, QLabel, QLineEdit, QPushButton, QFileDialog, QMessageBox, QComboBox, QSpinBox)
from PySide6.QtGui import QFont, QIcon
from PySide6.QtCore import Qt

class FolderCreatorApp(QWidget):
    def __init__(self):
        super().__init__()
        self.init_ui()

    def init_ui(self):
        self.setWindowTitle("CFolder")
        self.setGeometry(300, 200, 500, 300)
        
        # Add window icon
        self.setWindowIcon(QIcon('icon.ico'))  # مسیر فایل آیکون را بدهید

        # Fonts
        self.font = QFont("San Francisco", 12)
        self.setFont(self.font)

        # Layouts
        main_layout = QVBoxLayout()
        form_layout = QVBoxLayout()
        button_layout = QHBoxLayout()

        # Widgets
        self.label_base_name = QLabel("Base Name (Optional):")
        self.input_base_name = QLineEdit()

        self.label_count = QLabel("Number of Folders:")
        self.input_count = QSpinBox()
        self.input_count.setRange(1, 1000000)

        self.label_start_number = QLabel("Start Number:")
        self.input_start_number = QSpinBox()
        self.input_start_number.setRange(1, 1000000)

        self.label_path = QLabel("Select Destination Path:")
        self.input_path = QLineEdit()
        self.input_path.setText(os.path.expanduser("~/Desktop"))  # Default to Desktop
        self.btn_browse = QPushButton("Browse")
        self.btn_browse.clicked.connect(self.browse_folder)

        self.btn_create = QPushButton("Create Folders")
        self.btn_create.clicked.connect(self.create_folders)

        self.language_selector = QComboBox()
        self.language_selector.addItems(["English", "فارسی"])
        self.language_selector.currentIndexChanged.connect(self.switch_language)

        # Add widgets to form layout
        form_layout.addWidget(self.label_base_name)
        form_layout.addWidget(self.input_base_name)

        form_layout.addWidget(self.label_count)
        form_layout.addWidget(self.input_count)

        form_layout.addWidget(self.label_start_number)
        form_layout.addWidget(self.input_start_number)

        form_layout.addWidget(self.label_path)
        form_layout.addWidget(self.input_path)
        form_layout.addWidget(self.btn_browse)

        # Add buttons to button layout
        button_layout.addWidget(self.language_selector)
        button_layout.addWidget(self.btn_create)

        # Add layouts to main layout
        main_layout.addLayout(form_layout)
        main_layout.addLayout(button_layout)

        self.setLayout(main_layout)

    def browse_folder(self):
        folder = QFileDialog.getExistingDirectory(self, "Select Folder")
        if folder:
            self.input_path.setText(folder)

    def create_folders(self):
        base_name = self.input_base_name.text()
        count = self.input_count.value()
        start_number = self.input_start_number.value()
        path = self.input_path.text()

        if not os.path.exists(path):
            QMessageBox.critical(self, "Error", "The selected path does not exist.")
            return

        try:
            for i in range(count):
                folder_name = f"{base_name}{start_number + i}" if base_name else f"{start_number + i}"
                folder_path = os.path.join(path, folder_name)
                os.makedirs(folder_path, exist_ok=True)

            QMessageBox.information(self, "Success", f"{count} folders created successfully!")
        except Exception as e:
            QMessageBox.critical(self, "Error", f"An error occurred: {str(e)}")

    def switch_language(self):
        lang = self.language_selector.currentText()
        if lang == "فارسی":
            self.label_base_name.setText("اسم پایه (اختیاری):")
            self.label_count.setText("تعداد پوشه‌ها:")
            self.label_start_number.setText("شماره شروع:")
            self.label_path.setText("مسیر ذخیره‌سازی را انتخاب کنید:")
            self.btn_browse.setText("انتخاب مسیر")
            self.btn_create.setText("ایجاد پوشه‌ها")
        else:
            self.label_base_name.setText("Base Name (Optional):")
            self.label_count.setText("Number of Folders:")
            self.label_start_number.setText("Start Number:")
            self.label_path.setText("Select Destination Path:")
            self.btn_browse.setText("Browse")
            self.btn_create.setText("Create Folders")

if __name__ == "__main__":
    app = QApplication([])
    window = FolderCreatorApp()
    window.show()
    app.exec()
