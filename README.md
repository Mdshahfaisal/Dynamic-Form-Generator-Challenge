# Dynamic-Form-Generator-Challenge


1. Install Required Dependencies


bash
npm install react react-dom react-hook-form tailwindcss jsonlint typescript @types/react @types/react-dom


2. Setup Tailwind CSS

To set up Tailwind CSS, initialize it by running:

bash
npx tailwindcss init


Then configure your `tailwind.config.js` like this:

js
module.exports = {
  content: ["./src/**/*.{js,ts,jsx,tsx}"],
  theme: {
    extend: {},
  },
  plugins: [],
};


In your `src/index.css` (or `src/tailwind.css`), add:

css
@tailwind base;
@tailwind components;
@tailwind utilities;


3. Create JSON Schema TypeScript Interfaces

Define the types of the JSON schema to ensure type safety in TypeScript:

```ts
// src/types/FormSchema.ts
export interface FormField {
  id: string;
  type: 'text' | 'email' | 'select' | 'radio' | 'textarea';
  label: string;
  required: boolean;
  placeholder?: string;
  validation?: {
    pattern: string;
    message: string;
  };
  options?: { value: string; label: string }[];
}

export interface FormSchema {
  formTitle: string;
  formDescription: string;
  fields: FormField[];
}


4. Create the Main Application Component

Create a simplified version of the form generator that updates dynamically when the user modifies the JSON schema.

tsx
// src/App.tsx
import React, { useState } from 'react';
import { useForm } from 'react-hook-form';
import jsonlint from 'jsonlint';
import { FormSchema, FormField } from './types/FormSchema';

const App: React.FC = () => {
  const [schema, setSchema] = useState<FormSchema | null>(null);
  const { register, handleSubmit, formState: { errors } } = useForm();

  const onSubmit = (data: any) => {
    console.log('Form submitted:', data);
    alert('Form submitted successfully!');
  };

  const handleJsonChange = (e: React.ChangeEvent<HTMLTextAreaElement>) => {
    try {
      const parsedSchema = jsonlint.parse(e.target.value);
      setSchema(parsedSchema);
    } catch (e) {
      console.error('Invalid JSON');
    }
  };

  return (
    <div className="flex flex-col md:flex-row h-screen p-4">
      {/* JSON Editor */}
      <div className="w-full md:w-1/2 p-4">
        <textarea
          onChange={handleJsonChange}
          className="w-full h-80 border p-2"
          placeholder="Edit your JSON schema here..."
        ></textarea>
      </div>

      {/* Form Preview */}
      <div className="w-full md:w-1/2 p-4">
        {schema && (
          <form onSubmit={handleSubmit(onSubmit)}>
            <h1 className="text-2xl">{schema.formTitle}</h1>
            <p className="text-sm text-gray-600 mb-4">{schema.formDescription}</p>

            {schema.fields.map((field: FormField) => (
              <div key={field.id} className="mb-4">
                <label className="block text-sm">{field.label}</label>
                {field.type === 'text' || field.type === 'email' || field.type === 'textarea' ? (
                  <input
                    type={field.type}
                    placeholder={field.placeholder}
                    {...register(field.id, { required: field.required })}
                    className="w-full p-2 border rounded"
                  />
                ) : field.type === 'select' ? (
                  <select {...register(field.id, { required: field.required })} className="w-full p-2 border rounded">
                    {field.options?.map(option => (
                      <option key={option.value} value={option.value}>
                        {option.label}
                      </option>
                    ))}
                  </select>
                ) : field.type === 'radio' ? (
                  field.options?.map(option => (
                    <div key={option.value} className="flex items-center">
                      <input
                        type="radio"
                        value={option.value}
                        {...register(field.id, { required: field.required })}
                        className="mr-2"
                      />
                      {option.label}
                    </div>
                  ))
                ) : null}
                {errors[field.id] && <p className="text-red-500 text-sm">This field is required</p>}
              </div>
            ))}

            <button type="submit" className="px-4 py-2 bg-blue-600 text-white rounded">
              Submit
            </button>
          </form>
        )}
      </div>
    </div>
  );
};

export default App;

5. Testing the Application

For simplicity, this solution doesn't include the Playwright or Jest tests, but you can easily add tests later using Jest for unit tests and Playwright for end-to-end testing.

To add Playwright, run:

bash
npm install --save-dev playwright


For Jest:

bash
npm install --save-dev jest @types/jest ts-jest


6. Running the App

Ensure that your `tsconfig.json` is properly set up for React and TypeScript. Here's an example:

json
{
  "compilerOptions": {
    "target": "es5",
    "lib": ["dom", "es6"],
    "jsx": "react",
    "moduleResolution": "node",
    "esModuleInterop": true,
    "strict": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true
  }
}


To run the application:

bash
npm start



