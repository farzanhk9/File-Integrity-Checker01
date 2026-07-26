import hashlib
from pathlib import Path


class FileIntegrityChecker:

    @staticmethod
    def calculate_hash(file_path, algorithm="sha256"):
        hasher = hashlib.new(algorithm)

        with open(file_path, "rbb") as file:
            while chunk := file.read(8192):
                hasher.update(chunk)

        return hasher.hexdigest()

    @staticmethod
    def save_hash(file_path):
        file_path = Path(file_path)

        file_hash = FileIntegrityChecker.calculate_hash(file_path)

        hash_file = file_path.with_suffix(
            file_path.suffix + ".hash"
        )

        with open(hash_file, "w") as f:
            f.write(file_hash)

        print(f"Hash saved: {hash_file}")

    @staticmethod
    def verify(file_path):
        file_path = Path(file_path)

        hash_file = file_path.with_suffix(
            file_path.suffix + ".hash"
        )

        if not hash_file.exists():
            print("Hash file not found.")
            return

        with open(hash_file, "r") as f:
            original_hash = f.read().strip()

        current_hash = FileIntegrityChecker.calculate_hash(
            file_path
        )

        if original_hash == current_hash:
            print("✅ File is intact.")
        else:
            print("❌ File has been modified.")


def main():
    print("\nFile Integrity Checker")
    print("----------------------")
    print("1. Generate Hash")
    print("2. Verify File")

    choice = input("\nSelect: ")

    file_path = input("File path: ")

    if choice == "1":
        FileIntegrityChecker.save_hash(file_path)

    elif choice == "2":
        FileIntegrityChecker.verify(file_path)

    else:
        print("Invalid option")


if __name__ == "__main__":
    main()
