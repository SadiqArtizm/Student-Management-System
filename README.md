import inquirer from "inquirer";
    

interface Student {
    name: string;
    studentId: number;
    courses: string[];
    balance: number;
}

class StudentManagementSystem {
    private students: Student[] = [
        { name: 'ali', studentId: 67895, courses: ['Math', 'English'], balance: 500 },
        { name: 'aslam', studentId: 67890, courses: ['History', 'Science'], balance: 200 },
        { name: 'naveed', studentId: 67990, courses: ['sindhi', 'economics'], balance: 300 },
        { name: 'faisal', studentId: 67820, courses: ['english', 'Science'], balance: 250 },
        { name: 'sana', studentId: 68190, courses: ['urdu', 'pak study'], balance: 700 }
    ];

    async addStudent(): Promise<void> {
        const answers = await inquirer.prompt([
            {
                type: 'input',
                name: 'name',
                message: 'Enter student name:'
            }
        ]);

        const student: Student = {
            name: answers.name,
            studentId: this.generateStudentId(),
            courses: [],
            balance: 0
        };

        this.students.push(student);
        console.log(`Student ${student.name} added with ID: ${student.studentId}`);
    }

    async enrollStudent(): Promise<void> {
        const student = await this.selectStudent('Select a student to enroll in a course:');
        const answers = await inquirer.prompt([
            {
                type: 'input',
                name: 'course',
                message: 'Enter course to enroll:'
            }
        ]);

        student.courses.push(answers.course);
        console.log(`${student.name} has enrolled in ${answers.course}.`);
    }

    async viewBalance(): Promise<void> {
        const student = await this.selectStudent('Select a student to view balance:');
        console.log(`Balance for ${student.name}: ${student.balance}`);
    }

    async payTuition(): Promise<void> {
        const student = await this.selectStudent('Select a student to pay tuition:');
        const answers = await inquirer.prompt([
            {
                type: 'number',
                name: 'amount',
                message: 'Enter tuition amount to pay:'
            }
        ]);

        student.balance -= answers.amount;
        console.log(`${student.name} has paid $${answers.amount}. Remaining balance: $${student.balance}`);
    }

    async showStatus(): Promise<void> {
        const student = await this.selectStudent('Select a student to show status:');
        console.log(`Student Name: ${student.name}`);
        console.log(`Student ID: ${student.studentId}`);
        console.log('Courses Enrolled:');
        for (const course of student.courses) {
            console.log(`  - ${course}`);
        }
        console.log(`Balance: $${student.balance}`);
    }

    private generateStudentId(): number {
        return Math.floor(10000 + Math.random() * 90000);
    }

    private async selectStudent(message: string): Promise<Student> {
        const studentChoices = this.students.map(student => ({ name: student.name, value: student }));
        const answers = await inquirer.prompt([
            {
                type: 'list',
                name: 'selectedStudent',
                message: message,
                choices: studentChoices
            }
        ]);

        return answers.selectedStudent;
    }
}

async function main() {
    const studentManagementSystem = new StudentManagementSystem();

    while (true) {
        const answers = await inquirer.prompt([
            {
                type: 'list',
                name: 'choice',
                message: 'Student Management System',
                choices: [
                    'Add New Student',
                    'Enroll Student in Course',
                    'View Balance',
                    'Pay Tuition',
                    'Show Student Status',
                    'Exit'
                ]
            }
        ]);

        switch (answers.choice) {
            case 'Add New Student':
                await studentManagementSystem.addStudent();
                break;
            case 'Enroll Student in Course':
                await studentManagementSystem.enrollStudent();
                break;
            case 'View Balance':
                await studentManagementSystem.viewBalance();
                break;
            case 'Pay Tuition':
                await studentManagementSystem.payTuition();
                break;
            case 'Show Student Status':
                await studentManagementSystem.showStatus();
                break;
            case 'Exit':
                console.log('Exiting Student Management System.');
                return;
        }
    }
}

main();
